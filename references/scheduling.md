# Scheduling & recurring payments

## Tools

- `nakpix_schedule_payment(nome, chave, valor_brl, quando_ts, cupom, network)`
- `nakpix_schedule_recurring_payment(nome, chave, valor_brl, recurrencia,
  proxima_ts, intervalo_dias, cupom, network, data_fim, max_reps)`
- `nakpix_list_scheduled_payments`
- `nakpix_get_scheduled_payment(id)`
- `nakpix_update_scheduled_payment(id, …)`
- `nakpix_cancel_scheduled_payment(id)`
- `nakpix_pause_scheduled_payment(id)`
- `nakpix_resume_scheduled_payment(id)`
- `nakpix_get_schedule_history(id?)`

## Recurrence

`recurrencia` ∈ `once | daily | weekly | monthly | yearly | interval`.
Timestamps are absolute Unix seconds (convert the user's natural language date).

Examples:

- "Agenda R$ 100 para meu pai amanhã" → `nakpix_schedule_payment(…, quando_ts=tomorrow)`.
- "Todo dia 15 manda R$ 500" → `nakpix_schedule_recurring_payment(…, monthly, proxima_ts=next day-15)`.
- "Todo dia 5 paga o aluguel" → monthly at day 5.
- "Cancela o Pix mensal para João" → `nakpix_cancel_scheduled_payment`.

## Confirmation (mandatory)

Confirm **at creation**, covering all occurrences within the accepted
parameters:

```
Vou agendar um Pix <recorrência> de R$ <valor> para <nome>, <periodicidade>,
utilizando USDT na <rede> e seu cupom padrão. O pagamento será executado
automaticamente nas datas programadas, dentro dos limites definidos.
Deseja confirmar esse agendamento?
```

On the execution date, do NOT ask again. If the user changes value/recipient/
limits later, re-confirm that change.

## Execution

The scheduler runs in the background (independent of any open conversation),
checks balance/quote/coupon/limits, executes, records the result and notifies
the user. If the signing wallet is only on a computer that is off, do not
promise execution while it is offline — and never move seeds/keys to the server
to work around that.

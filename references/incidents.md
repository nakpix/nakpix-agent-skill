# Notifications & incidents

## Proactive notifications

The MCP persists events in a local queue. When you (re)connect, call
`nakpix_get_notifications` and surface any pending:

- `scheduled_result` — a scheduled payment ran (success/failure/pending).
- `scheduled_resolved` — a previously-pending scheduled payment reached a
  definitive result.
- `reminder` — a reminder-only schedule fired.
- `incident` — an unresolved-payment incident was opened.

Delivery respects the client's capability: if proactive push isn't available,
events wait for the next reconnect.

## 15-minute unresolved incident

Trigger: crypto was sent, but the PIX is not confirmed and no refund is
confirmed, for 15+ minutes (or a rejected PIX whose funds remain unresolved).

The monitor:

1. Opens an incident (deduplicated by order id) in state `email_state=pending`.
2. Enqueues an `incident` notification carrying the full operational data:
   `to` (suporte@nakswap.xyz), `subject`, `body`, `incident_id`, `ordem_id`.
3. Keeps tracking until a definitive result.

## The agent's job (email delivery)

When you surface an `incident` notification:

1. If you have an email tool, send `body` to `to` with `subject` as the subject,
   then call `nakpix_report_incident_email(incident_id, "sent")`.
2. If you do NOT have an email tool, show the user `to` + `subject` + `body` so
   they can copy and send, then call
   `nakpix_report_incident_email(incident_id, "instructed")`.
3. If the send fails, call `nakpix_report_incident_email(incident_id, "failed")`
   — the monitor re-enqueues it (up to a few attempts).

Email states: `pending` (queued), `sent` (accepted by the email service),
`instructed` (user told to send manually), `failed` (gave up after retries).
Never report `sent` before the email service actually accepted it.

The monitor guarantees **exactly one in-flight `incident` notification per
incident**: it skips enqueueing when one is already pending, and a reported
`failed` attempt clears the old notification before resetting to `pending`, so
a retry never queues a duplicate report.

## Refund is not a completed PIX

A `scheduled_result` / `scheduled_resolved` notification whose `status` is
`refunded` (or `extornado`) means the money was estornado — it is NOT a
successful PIX. Report it as a refund and never as "Pix pago com sucesso".
Only `sent`/`done`/`solucionado`/`resolved` mean the PIX actually landed.

## What the agent tells the user

```
⚠️ Identificamos um problema no seu pagamento Pix.

A transferência de criptomoeda foi identificada, mas o Pix ainda não foi
confirmado e não há estorno confirmado após 15 minutos.

Transação NAKPIX: [CÓDIGO]

Enviei os dados da operação por e-mail para acompanhamento.

Não tente repetir esse pagamento antes de verificarmos o resultado, para
evitar duplicidade.

Continuaremos acompanhando a transação.
```

## Hard rules

- Do NOT re-send the transfer automatically.
- Do NOT mark an unknown result as a definitive failure.
- Do NOT claim a refund happened without confirmation.
- Do NOT promise an automatic refund or declare money lost.
- Send the incident email yourself (your own email tool) to the notification's
  `to` address, then report the outcome with `nakpix_report_incident_email`.
  If you can't send email, report `instructed` and show the user the report —
  never report `sent` before the email service accepts it.
- On later resolution, update the user with the real receipt or confirmed refund.

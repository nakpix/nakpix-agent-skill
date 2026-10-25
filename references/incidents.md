# Notifications & incidents

## Proactive notifications

The MCP persists events in a local queue. When you (re)connect, call
`nakpix_get_notifications` and surface any pending:

- `scheduled_result` — a scheduled payment ran (success/failure/pending).
- `reminder` — a reminder-only schedule fired.
- `incident` — an unresolved-payment incident was opened.

Delivery respects the client's capability: if proactive push isn't available,
events wait for the next reconnect.

## 15-minute unresolved incident

Trigger: crypto was sent, but the PIX is not confirmed and no refund is
confirmed, for 15+ minutes (or a rejected PIX whose funds remain unresolved).

The monitor:

1. Opens an incident (deduplicated by order id).
2. Enqueues an `incident` notification carrying the full operational data
   (`subject` + `body`). The AGENT sends the email with its own email tool —
   the MCP never uses SMTP.
3. Keeps tracking until a definitive result.

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
- Send the incident email yourself from the notification's `subject` + `body`
  (use your own email tool). If you can't send email in this client, tell the
  user the alert is still pending delivery — don't claim it was sent.
- On later resolution, update the user with the real receipt or confirmed refund.

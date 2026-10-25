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
2. Emails `suporte@nakswap.xyz` with all operational data (no seeds/keys).
3. Notifies the user.
4. Keeps tracking until a definitive result.

## What the agent tells the user

```
⚠️ Identificamos um problema no seu pagamento Pix.

A transferência de criptomoeda foi identificada, mas o Pix ainda não foi
confirmado e não há estorno confirmado após 15 minutos.

Transação NAKPIX: [CÓDIGO]

Um alerta com os dados da operação foi encaminhado ao suporte da NAKPIX.

Não tente repetir esse pagamento antes de verificarmos o resultado, para
evitar duplicidade.

Continuaremos acompanhando a transação.
```

## Hard rules

- Do NOT re-send the transfer automatically.
- Do NOT mark an unknown result as a definitive failure.
- Do NOT claim a refund happened without confirmation.
- Do NOT promise an automatic refund or declare money lost.
- If the email failed to send, say the alert is pending delivery — don't claim
  it was sent.
- On later resolution, update the user with the real receipt or confirmed refund.

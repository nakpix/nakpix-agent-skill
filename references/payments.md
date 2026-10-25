# Payments (immediate PIX)

## Flow

1. `nakpix_validate_pix(chave)` — reject malformed keys.
2. `nakpix_get_quote(pix_value, cupom, network)` — full cost breakdown.
3. Present the confirmation card. Wait for the user's explicit yes.
4. `nakpix_pay_pix(pix_value, chave, cupom, network, confirm=True)`.
5. Report the real result.

## Confirmation card

```
NAKPIX — PAGAMENTO PIX

Destinatário: <nome>
Chave Pix: <chave>
Valor: R$ <valor>

Saldo: <saldo> USDT
Custo total: <usdt_necessario> USDT
Cotação: <cambio> BRL/USDT
Fee: <fee_pct>%
Cupom: <cupom ou —>
Rede: <polygon|solana>

Deseja enviar?
```

## Confirmation preference

- Default: `payment_confirmation_enabled = true`.
- The user can disable it ("Desativa a confirmação de Pix"). Then an explicit
  instruction with unambiguous amount + recipient may run directly.
- Persist via `nakpix_set_payment_confirmation`.

## Results

- **Success**: return the order id, amount, recipient, blockchain, token,
  coupon, timestamp, confirmed status and the real receipt (`nakpix_get_receipt`).
- **Failure**: return the real reason (invalid key, key not found, insufficient
  balance, bank rejection, expired cobrança, blockchain failure, provider
  problem). Never invent a reason.
- **Pending**: "Seu Pix continua em processamento. Código NAKPIX: [CÓDIGO]".
  Poll with `nakpix_get_payment_status`. On timeout, do NOT re-send.

## Duplicate prevention

Do not call `nakpix_pay_pix` twice for the same operation. The backend order
id and the local payments table are idempotent — rely on them.

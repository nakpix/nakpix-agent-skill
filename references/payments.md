# Payments (immediate PIX)

## Flow

1. `nakpix_validate_pix(chave)` — reject malformed keys.
2. `nakpix_get_quote(pix_value, cupom, network)` — full cost breakdown.
3. Confirmation:
   - Default (`payment_confirmation_enabled = true`): present the confirmation
     card, wait for the user's explicit yes, then
     `nakpix_pay_pix(..., confirm=True)`.
   - Disabled (`payment_confirmation_enabled = false`): the user opted out —
     still show what you're sending, then
     `nakpix_pay_pix(..., confirm=False)`.
4. `nakpix_pay_pix(pix_value, chave, cupom, network, confirm=…)`.
5. Report the real result.

> **Network:** PIX execution is **Polygon-only** today. Solana supports wallet
> creation and balance/deposit queries but NOT `nakpix_pay_pix`. Never announce
> Solana PIX as ready; never call `nakpix_pay_pix(network="solana")`.

## Confirmation card

> Show the **complete** value for everything — PIX key, tx hash, address,
> order ID, resgate code, value/cost. Never truncate, ellipsize or abbreviate
> (e.g. `1dcc8bad-…9960`). The user must be able to verify the exact
> destination and transaction before money moves.

```
NAKPIX — PAGAMENTO PIX

Destinatário: <nome>
Chave Pix: <chave completa, sem truncar>
Valor: R$ <valor>

Saldo: <saldo> USDT
Custo total: <usdt_necessario> USDT
Cotação: <cambio> BRL/USDT
Fee: <fee_pct>%
Cupom: <cupom ou —>
Rede: polygon

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

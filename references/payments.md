# Payments (immediate PIX)

## Flow

1. `nakpix_validate_pix(chave)` — reject malformed keys.
2. `nakpix_get_quote(pix_value, cupom, network)` — cotação (só campos de cliente).
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
PIX para <nome>

• Chave: <chave completa, sem truncar>
• Valor PIX: R$ <pix_brl, 2 casas>
• Câmbio: 1 USDT = R$ <cambio, 4 casas>
• Custo em USDT: <usdt_necessario, 4 casas>
• Cupom aplicado: <codigo> (<desconto>% desconto)          ← omitir se sem cupom
• Taxa NAKPIX: <fee_base>% - <desconto>% (cupom <codigo> <desconto>%) = <fee_final>%
  (sem cupom, mostrar só: Taxa NAKPIX: <fee_base>%)
• Rede: Polygon (saldo USDT: <saldo> ✓)

Confirma o pagamento? Digo sim que eu executo.
```

> A cotação (`nakpix_get_quote`) devolve os campos de cliente: `pix_brl`,
> `usdt_necessario`, `cambio` (1 USDT = R$ X), `cupom`, `desconto`, `fee_base`
> e `fee_final`. O card mostra exatamente isso — câmbio, custo em USDT, cupom
> com o desconto e a conta da taxa (base − desconto = final).
> **Nunca** mostre custo interno (o provedor), total bruto ou margem — isso é
> informação interna da NakSwap e nem chega ao MCP. Formato dos números em
> pt-BR (vírgula decimal): BRL com 2 casas, USDT e câmbio com 4 casas.

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

# Boleto rules (linha digitável) — for agent integrations

Same rules the production frontend enforces (nakpix.xyz). The MCP tools
implement ALL of this — the agent just needs to know what to expect.

## Line types (after stripping non-digits)

| | Bancário | Convênio/Arrecadação |
|---|---|---|
| Digits | 47 | 48 |
| What | store, card, school, services | water, power, gas, phone, tax, fine |
| Amount | last 10 digits (pos 38-47) ÷ 100 | campo1[5:11] + campo2[0:4] ÷ 100 |
| Check digits | 3 fields (9/10 d) mod 10 + general DV mod 11 | 4 fields of 11 — mod 10 for type 6/7, mod 11 for 8/9 |
| Due date | fator de vencimento (pos 34-37) | NOT universally extractable |

A line starting with `00020` is a PIX BR Code, not a boleto. PIX keys are
never 47/48 digits — auto-detect boleto on 47/48 digits.

## Amount rules

- The payment amount comes FROM THE LINE. Never invent or accept a
  different amount from the user for a line that carries one.
- **NEVER read campo 4 (l[36:47]) on a convênio line** — that's the single
  most common wrong implementation (returns a garbage R$ 6 million value).
- **Zero-amount bancário lines are REAL** (value = `0000000000`): the payer
  states the amount at payment time. The tool requires `valor_brl` for
  these and injects it into the line BEFORE creating the order:
  `cents → pos 38-47` + general DV (mod 11) recalculated; fator and field
  DVs untouched. Production case: synthetic bank line
  — line left the bank with zero and the provider would pay R$ 0,00 and
  never settle.
- Convênio lines carry their amount in their own field — never touch them.

## Due date rules

- Bancário: fator = days from 1997-10-07; **fator ≥ 1000 counts from
  2025-02-22** (FEBRABAN reset after the 9999 overflow); fator 0000 = no
  due date (payable anytime).
- **Expired bancário boleto → REFUSE the payment** ("This boleto is
  expired — only boletos within their validity period are accepted"),
  always showing amount + due date so the user sees why.
- Convênio has NO universal due date → never block on expiry (show N/A).
- Juros/multa are computed by the bank at payment time — the line carries
  only the nominal amount. You can detect "expired", never the penalty.

## Payment flow (PIX rail, crypto off-ramp)

1. `nakpix_parse_boleto(linha)` — validate + extract (read-only).
2. `nakpix_quote_boleto(linha, valor_brl?)` — cost in USDT (+ coupon).
3. User approves amount + due date + total cost.
4. `nakpix_pay_boleto(linha, valor_brl?, cupom?, confirm=True)` — creates
   the order with `chave="[BOLETO]"` + `brcode=<linha>`, funds it from
   the local wallet and polls to conclusion.

The boleto line travels ENCRYPTED (RSA-OAEP) inside the on-chain memo —
`MEMO:<linha>` with no separate amount; the provider reads the amount from
the line itself. The client's crypto charge is the quoted cost, separate
from what the provider pays.

## Response shape

`nakpix_pay_boleto` returns `status`, the usual payment fields (`ordem`
with `BOL*` prefix, `tx_hash`, `usdt`, `resgate`…) plus a `boleto` block:
`{valor, vencimento, tipo, linha}` (linha = the ready-to-pay line, with
the amount injected when the original was zero-amount).
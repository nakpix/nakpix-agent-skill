# Deposits & balances

## Checking balances

`nakpix_get_balances` returns the real on-chain balance:

- Polygon: `usdt` + `native` (POL).
- Solana: `usdt` + `native` (SOL).

Always report the balance actually available — never an estimate.

## Gas check before paying

If the wallet has USDT but no POL/SOL for gas, warn the user and block the
attempt:

```
Sua carteira tem USDT, mas não tem POL/SOL suficiente para pagar a taxa de
rede. Deposite <POL|SOL> antes de continuar.
```

## Deposits

`nakpix_get_deposits` lists recent incoming transfers. Confirm the token and
network match the configured wallet — never suggest sending USDT on a network
different from the one configured.

## Tokens (official)

- Polygon USDT: `0xc2132d05d31c914a87c6611c10748aeb04b58e8f`.
- Solana USDT mint: `Es9vMFrzaCERmJfrF4H2FYD4KCoNkY11McCe8BenwNYB`.

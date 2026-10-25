# Troubleshooting

## "carteira não desbloqueada"

The wallet-agent can't decrypt. Either:

- `nakpix-wallet-agent unlock` (interactive) to set the passphrase, or
- set `NAKPIX_UNLOCK_PASSPHRASE` for the local signing component.

## Balance shows 0 / RPC timeout

- Check the RPC env (`POLYGON_RPC_URL`, `SOLANA_RPC_URL`).
- The balance tools are read-only and fall back across providers.

## Quote/order returns an error

- Confirm `NAKPIX_BACKEND_URL` is correct and reachable.
- The backend may be in maintenance or rate-limited (retry later).

## Payment blocked with `confirm=True` still failing

- Verify the key with `nakpix_validate_pix`.
- Check USDT and gas balances (`nakpix_get_balances`).
- Verify the coupon (`nakpix_validate_coupon`).

## Scheduled payment didn't run

- The `nakpix-scheduler` background service must be running.
- The signing wallet must be reachable/unlocked on that machine.
- Check `nakpix_get_schedule_history` for occurrence errors.

## Wallet recovery

- `nakpix-wallet-agent reveal <network>` (shows words + derivation path).
- `nakpix-wallet-agent restore <network> backup.json` for an encrypted backup.

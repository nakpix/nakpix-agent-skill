# Coupons (default + per-operation)

## Tools

- `nakpix_set_default_coupon(codigo)`
- `nakpix_get_default_coupon`
- `nakpix_remove_default_coupon`
- `nakpix_validate_coupon(codigo)`

## Behavior

- The default coupon is persisted and applied automatically to eligible
  payments; a per-operation coupon overrides it.
- Coupon data (fee %, discount, active status) is read LIVE from the backend
  coupon DB on every quote/validation — the MCP never caches or hard-codes
  coupon values. A coupon's `%` can change (or be deactivated) at any time, so
  always validate against the backend (same rules as the frontend).
- `nakpix_validate_coupon` returns ONLY `codigo` + `desconto` (the user-facing
  discount). The fee `pct` (final fee) and `fee_base` (base fee) come from the
  backend inside the quote (`nakpix_get_quote`); the commission wallet and the
  usage count never surface.
- The backend computes commissions; commissions are released only after the PIX
  is definitively confirmed and reconciled — never for pending/rejected/
  expired/unreconciled operations.

## Safety

Do not hard-code real coupons in public docs or the skill. Coupons are personal.

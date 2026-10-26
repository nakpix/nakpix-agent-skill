# Coupons (default + per-operation)

## Tools

- `nakpix_set_default_coupon(codigo)`
- `nakpix_get_default_coupon`
- `nakpix_remove_default_coupon`
- `nakpix_validate_coupon(codigo)`

## Behavior

- The default coupon is persisted and applied automatically to eligible
  payments; a per-operation coupon overrides it.
- `nakpix_validate_coupon` checks against the backend (source of truth) and
  returns ONLY the effective fee `pct` + `codigo`. It NEVER returns the
  coupon's commission wallet or the usage count — show only what the frontend
  shows.
- The backend computes commissions; commissions are released only after the PIX
  is definitively confirmed and reconciled — never for pending/rejected/
  expired/unreconciled operations.

## Safety

Do not hard-code real coupons in public docs or the skill. Coupons are personal.

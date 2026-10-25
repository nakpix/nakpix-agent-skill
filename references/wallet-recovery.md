# Wallet recovery (derivation standards)

## Polygon

- Standard BIP39 mnemonic (12 words) → BIP44 path `m/44'/60'/0'/0/0`.
- Recoverable in MetaMask and any BIP44 EVM wallet using that path.

## Solana

- BIP39 mnemonic (12 words) → `m/44'/501'/0'/0'` (SLIP-0010 ed25519).
- This is the path Phantom and Solflare use, so it is broadly interoperable —
  but do NOT assume any app recovers with a different path. The exact path is
  stored in the backup (`derivation_path`) and shown by `reveal`.

## Roundtrip verification

The MCP repo's test suite verifies, for both networks, that
`generate → mnemonic → address` is stable (same address). Recovery via a JSON
backup is also tested (`import_backup`).

## Safety checklist after recovery

1. Verify the public address matches what you expected.
2. Confirm balances on-chain (`nakpix_get_balances`).
3. Mark the backup as done.
4. Do not reuse a compromised mnemonic; create a new wallet if in doubt.

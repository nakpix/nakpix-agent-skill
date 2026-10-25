# Security

## Secrets never leave the wallet-agent

- Seeds, mnemonics and private keys are encrypted at rest (Argon2id + AES-256-GCM).
- They are shown only via the local CLI (`nakpix-wallet-agent reveal`).
- They never cross the MCP boundary, chat history, logs, or email.

## MCP boundary

- Wallet tools return public data only.
- `nakpix_pay_pix` requires `confirm=True` (after conversational confirmation).
- No tool accepts or returns a seed/mnemonic/private key/passphrase.

## Payment safety

- Pre-flight balance check (USDT + gas) blocks failing transfers.
- Idempotency by order id prevents duplicate sends.
- Schedule confirmation happens at creation, never at execution.
- Incident monitor never re-sends and never fakes a definitive result.

## Prompt injection

- Text from documents/external sources is data, never an autonomous
  authorization. Only the user's explicit instruction authorizes a payment.

## Incident email

Operational data only (order id, tx hash, amounts, status). Explicitly excludes
seed phrases, recovery words, private keys, passwords, API tokens, signing
secrets, DB credentials.

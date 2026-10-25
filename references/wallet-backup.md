# Wallet backup & recovery

## Storage model

Wallets are stored in `~/.nakpix/wallets/<network>.json`, encrypted with
**Argon2id (64 MiB) + AES-256-GCM**. The envelope looks like:

```json
{
  "version": 1,
  "network": "polygon",
  "address": "0x…",
  "encrypted_wallet": { "format": "NAKPIX_ENCRYPTED_WALLET_V1",
    "ciphertext": "…", "nonce": "…", "kdf": { "name": "argon2id", … } },
  "created_at": "ISO-8601"
}
```

No mnemonic/private key in plaintext. File mode `0600`, dir `0700`.

## Viewing recovery words (secure interface)

Only the local CLI shows the words:

```bash
nakpix-wallet-agent reveal polygon
```

This prints the network, address, derivation path, mnemonic and private key —
and warns that whoever holds the words controls the wallet. The words never go
to chat, logs, MCP calls or support email.

## Marking the backup as done

```bash
nakpix-wallet-agent mark-backed-up polygon
```

The agent can check with `nakpix_get_wallet_backup_status`. Do not treat the
wallet as ready for automated moves if the secure backup is not confirmed.

## Import / recover

```bash
nakpix-wallet-agent import polygon                # prompts for the seed/key (hidden)
nakpix-wallet-agent export polygon                # encrypted JSON backup
nakpix-wallet-agent restore polygon backup.json
```

Import from a mnemonic (12 words) or a private key (hex). The `import`
command reads the secret interactively (hidden input) so it never lands in the
shell history — omit the secret from the command line. Importing refuses to
overwrite an existing wallet. After restoring, verify the public address.

The recovery words are provided directly to the wallet-agent CLI, never to the
model context.

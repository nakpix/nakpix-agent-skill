# Wallet creation (first use)

The first time the user asks for a PIX, check `nakpix_wallet_status`. If no
wallet exists, offer Polygon or Solana and create with `nakpix_create_wallet`.

## Polygon

- EVM wallet, derivation path `m/44'/60'/0'/0/0` (same path MetaMask uses).
- BIP39 12-word mnemonic.
- Public address is a `0x…` (42 chars).
- Deposit **USDT** (Polygon) to fund payments; keep **POL** for gas.

## Solana

- Derivation `m/44'/501'/0'/0'` (standard Phantom/Solflare path).
- BIP39 12-word mnemonic.
- Public address is base58 (43–44 chars).
- Deposit **USDT** (Solana mint `Es9vMFrzaCERmJfrF4H2FYD4KCoNkY11McCe8BenwNYB`);
  keep **SOL** for fees.

## What the agent tells the user

```
Carteira <Polygon|Solana> criada com sucesso!
Seu endereço público é: <ADDRESS>

Para realizar pagamentos Pix pela NAKPIX, deposite:
- USDT na rede <rede>, para financiar os pagamentos.
- <POL|SOL> na rede <rede>, para pagar as taxas da rede.

Seu backup seguro está pronto — abra a interface segura da carteira para
visualizar e guardar suas palavras de recuperação:
  nakpix-wallet-agent reveal <polygon|solana>
```

The agent NEVER echoes or requests the mnemonic itself. It only points the user
to the local CLI.

## Multiple networks

- If only Polygon exists and the user asks for Solana, offer to create/import a
  Solana wallet (`nakpix_create_wallet("solana")`).
- If a wallet already exists, do NOT create another automatically.

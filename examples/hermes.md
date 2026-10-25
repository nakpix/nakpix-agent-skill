# Hermes — NAKPIX MCP config

Hermes (Nous Research) supports MCP servers in `config.yaml`:

```yaml
mcp:
  servers:
    nakpix:
      command: nakpix-mcp
      env:
        NAKPIX_BACKEND_URL: https://nakpix.xyz
```

The NAKPIX skill (`SKILL.md`) can be installed as a Hermes skill so the agent
follows the full flow (first-use wallet, confirmation, scheduling, incidents).

Example:

```
> Faz um Pix de R$ 250 pra minha filha
```

The agent checks `nakpix_wallet_status`, then walks the flow described in
`references/`.

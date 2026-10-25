# Generic MCP client — NAKPIX config

Any MCP-compatible client (stdio transport):

```json
{
  "mcpServers": {
    "nakpix": {
      "command": "nakpix-mcp",
      "args": [],
      "env": {
        "NAKPIX_BACKEND_URL": "https://nakpix.xyz/relay",
        "NAKPIX_BACKEND_SOLANA": "https://nakpix.xyz/relay-sol-prod",
        "NAKPIX_HOME": "~/.nakpix"
      }
    }
  }
}
```

Requirements:

- `nakpix-mcp` on `PATH` (pip install -e the MCP repo).
- `NAKPIX_BACKEND_URL` set to the NAKPIX backend.
- Background services (`nakpix-scheduler`, `nakpix-incidents`) running for
  scheduled payments and the 15-minute incident monitor.

The companion skill teaches the agent the complete behavior (wallets, balances,
contacts, coupons, payments, scheduling, document reading, incidents).

# OpenCode — NAKPIX MCP config

OpenCode reads MCP servers from `opencode.json` (project or global):

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "nakpix": {
      "type": "local",
      "command": ["nakpix-mcp"],
      "environment": {
        "NAKPIX_BACKEND_URL": "https://nakpix.xyz"
      }
    }
  }
}
```

Then:

```
> paga uma cobrança de R$ 149,90 que está nesse PDF, agenda pro vencimento
```

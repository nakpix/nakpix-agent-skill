# OpenClaw — NAKPIX MCP config

Register the NAKPIX MCP server as a stdio tool. Add to your OpenClaw agent
configuration (claw config / agents.yml):

```yaml
mcp:
  nakpix:
    command: nakpix-mcp
    env:
      NAKPIX_BACKEND_URL: https://nakpix.xyz
      NAKPIX_HOME: /home/<user>/.nakpix
```

Or via the generic JSON form OpenClaw accepts:

```json
{ "mcpServers": { "nakpix": {
    "command": "nakpix-mcp",
    "env": { "NAKPIX_BACKEND_URL": "https://nakpix.xyz" }
} } }
```

Then, in an OpenClaw chat:

```
You: quero pagar um Pix de R$ 100 pro meu pai
```

The agent will detect no wallet and offer Polygon/Solana.

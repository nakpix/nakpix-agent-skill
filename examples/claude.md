# Claude (Claude Desktop / Code) — NAKPIX MCP config

Add to `~/.config/Claude/claude_desktop_config.json` (or the equivalent for
Claude Code / `claude mcp add`):

```json
{
  "mcpServers": {
    "nakpix": {
      "command": "nakpix-mcp",
      "env": {
        "NAKPIX_BACKEND_URL": "https://nakpix.xyz"
      }
    }
  }
}
```

Or with `claude mcp add`:

```bash
claude mcp add nakpix -- nakpix-mcp
```

Install the `nakpix-agent-skill` as a Claude skill so the model follows the
full NAKPIX flow.

Example:

```
Paga R$ 100 pro meu pai.
```

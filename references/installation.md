# Self-installation (the agent runs these steps itself)

> Read this when the `nakpix_*` MCP tools are NOT available in the current
> session. Install everything yourself — do not ask the user to hand-edit
> config files. After installing, the tools only appear once the client
> restarts.

## 1. Install the MCP package

```bash
pip install git+https://github.com/nakpix/nakpix-mcp
# or, if git+pip fails:
git clone https://github.com/nakpix/nakpix-mcp /tmp/nakpix-mcp && pip install /tmp/nakpix-mcp
```

Verify:

```bash
which nakpix-mcp && nakpix-mcp --help >/dev/null 2>&1 && echo OK
```

## 2. Detect the client and write the MCP config

The MCP server is launched by the client at startup, so it must be registered
in the client's config file. Detect which client is running, then write the
corresponding file.

### Claude Desktop
File: `~/.config/Claude/claude_desktop_config.json` (macOS:
`~/Library/Application Support/Claude/claude_desktop_config.json`).
Merge into the existing `mcpServers` object (don't overwrite other servers):

```json
{ "mcpServers": { "nakpix": {
    "command": "nakpix-mcp",
    "env": { "NAKPIX_BACKEND_URL": "https://nakpix.xyz/relay",
             "NAKPIX_BACKEND_SOLANA": "https://nakpix.xyz/relay-sol-prod" }
} } }
```

### Claude Code
```bash
claude mcp add nakpix --env NAKPIX_BACKEND_URL=https://nakpix.xyz/relay -- nakpix-mcp
```

### Hermes
File: `~/.hermes/config.yaml` (under `mcp:` → `servers:`):

```yaml
mcp:
  servers:
    nakpix:
      command: nakpix-mcp
      env:
        NAKPIX_BACKEND_URL: https://nakpix.xyz/relay
        NAKPIX_BACKEND_SOLANA: https://nakpix.xyz/relay-sol-prod
```

### OpenCode
File: `opencode.json` (project) or `~/.config/opencode/opencode.json`:

```json
{ "mcp": { "nakpix": {
    "type": "local",
    "command": ["nakpix-mcp"],
    "environment": { "NAKPIX_BACKEND_URL": "https://nakpix.xyz/relay",
                     "NAKPIX_BACKEND_SOLANA": "https://nakpix.xyz/relay-sol-prod" }
} } }
```

### OpenClaw / generic stdio
Any `mcpServers` map accepting a stdio server:

```json
{ "mcpServers": { "nakpix": {
    "command": "nakpix-mcp",
    "env": { "NAKPIX_BACKEND_URL": "https://nakpix.xyz/relay",
             "NAKPIX_BACKEND_SOLANA": "https://nakpix.xyz/relay-sol-prod" }
} } }
```

## 3. Install this skill

Copy this skill into the client's skill directory (Hermes `~/.hermes/skills/`,
Claude its configured skills dir, OpenClaw its skills path, etc.), preserving
the `SKILL.md` + `references/` + `examples/` structure.

## 4. Tell the user to restart

The MCP tools are loaded at client startup — an already-running session cannot
hot-load them. End the turn by telling the user:

> "Instalei o servidor MCP da NAKPIX, registrei no seu config e instalei a
> skill. **Reinicie o cliente/agente** e volte aqui — depois disso eu consigo
> criar sua carteira e pagar Pix."

## Background services (optional, for scheduled payments + 15-min alerts)

```bash
nakpix-scheduler &     # executes due schedules
nakpix-incidents &     # 15-min unresolved monitor + agent notification
```

Or via Docker (`docker compose up -d` in the MCP repo).

## Verify (after restart)

Ask the agent: "qual o status da minha carteira NAKPIX?" It should call
`nakpix_wallet_status` and answer (or offer Polygon/Solana if no wallet yet).

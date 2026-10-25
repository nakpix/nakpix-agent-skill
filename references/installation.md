# Installation & configuration

## Prerequisites

- Python 3.10+ (tested on 3.12/3.13).
- An MCP-compatible agent client (OpenClaw, Hermes, Claude, OpenCode, …).
- NAKPIX backend URL (provided by NAKPIX; the backend itself is private).

## Install the MCP server

```bash
git clone https://github.com/nakpix/nakpix-mcp
cd nakpix-mcp
python -m venv .venv && source .venv/bin/activate
pip install -e .
```

## Configure

```bash
cp .env.example .env
```

Minimum required:

| Var | Purpose |
|---|---|
| `NAKPIX_BACKEND_URL` | NAKPIX backend relay URL (https) — MUST include the path, e.g. `https://nakpix.xyz/relay` (Polygon) / `https://nakpix.xyz/relay-sol-prod` (Solana). The bare domain returns frontend HTML, not relay JSON. |
| `NAKPIX_SMTP_HOST` / `_USER` / `_PASS` | SMTP for incident emails (optional but recommended) |
| `POLYGON_RPC_URL` / `SOLANA_RPC_URL` | balance/deposit RPCs |

`NAKPIX_HOME` (default `~/.nakpix`) is where wallets, contacts, settings, the
SQLite DB and logs live.

## Register with your agent client

The MCP server speaks stdio. See `examples/` for the exact config per client.

Generic (stdio):

```json
{ "mcpServers": { "nakpix": {
    "command": "nakpix-mcp",
    "env": { "NAKPIX_BACKEND_URL": "https://nakpix.xyz" }
} } }
```

## Background services

Scheduled/recurring payments and the 15-minute incident monitor need these to
run independently of any open chat:

```bash
nakpix-scheduler
nakpix-incidents
```

Or via Docker (see the MCP repo's `docker-compose.yml`).

## Verify

Ask your agent: "what's my NAKPIX wallet status?" It should answer with the
wallet list (or that none is configured yet).

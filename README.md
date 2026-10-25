# NAKPIX Agent Skill

The official agent skill for **NAKPIX** — pay PIX with crypto (USDT on
Polygon/Solana) using AI agents (OpenClaw, Hermes, Claude, OpenCode and other
MCP-compatible clients), without a conventional account.

This skill teaches the agent the complete flow. It pairs with the
[NAKPIX MCP server](https://github.com/nakpix/nakpix-mcp), which exposes the
tools (wallets, balances, contacts, coupons, payments, scheduling, incidents).

## What it covers

- First-use wallet detection + Polygon/Solana creation.
- Secure backup of recovery words (local CLI only).
- Balances and deposits (USDT + gas).
- PIX contact agenda.
- Default/permanent coupons.
- Immediate PIX with quote + configurable confirmation.
- Scheduled and recurring payments (confirmed once, at creation).
- Reading bills from image/PDF/QR/text (due dates, expired cobranças).
- Receipts and real failure reasons.
- 15-minute unresolved-payment incidents + support email.

## Structure

```
SKILL.md                 # the agent's instruction set (start here)
references/              # detailed docs per topic
examples/                # MCP config per client
```

## Install

1. Install the MCP server (see its README).
2. Register the MCP server with your client (see `examples/`).
3. Install this skill in your agent's skill directory.

## License

MIT — see [LICENSE](LICENSE).

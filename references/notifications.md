# Notifications (delivery)

The MCP persists proactive events locally. Delivery depends on the client:

- **Push-capable** (Telegram bot, etc.): deliver immediately when the event is
  produced by the scheduler/incident monitor.
- **Pull / reconnect** (stdio agents): `nakpix_get_notifications` returns the
  pending events when the agent reconnects.

The agent should check for pending notifications at the start of a session that
follows any scheduled payment or incident window.

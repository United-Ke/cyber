# Comrades Cyber Bot — Render Free deployment

## Render settings
- Create a Web Service from this repository (or use `render.yaml` as a Blueprint).
- Runtime: Node; build: `npm install`; start: `npm start`; health check: `/health`; plan: Free.

## Required environment variables
Set in Render → service → Environment. Never commit real values.

| Variable | Value |
|---|---|
| `NODE_ENV` | `production` |
| `BOT_MODE` | `private` |
| `BOT_PREFIX` | `.` |
| `BOT_NAME` | `Comrades Cyber` |
| `BOT_TIMEZONE` | `Africa/Nairobi` |
| `OWNER_NUMBER` | Your WhatsApp number, international format, digits only (e.g. 2547XXXXXXXX) |
| `SESSION_ID` | Your valid Comrades Cyber session string |

Add optional API keys only for features you actively use.

## Private-access behavior
When `BOT_MODE=private`, the message event handler ignores non-owner incoming messages before command and status/group automation runs. The existing owner/JID manager is used to identify the owner. Set `OWNER_NUMBER` correctly and test using a second WhatsApp account.

## Free-plan limitations
- Render Free services can spin down after inactivity. A WhatsApp socket can disconnect while asleep. This package does not add artificial pings or bypass free-plan limits.
- Render Free filesystem storage is ephemeral. The bot's evolving WhatsApp auth/session keys and SQLite data may be lost after redeploys, restarts, or instance replacement. Keep the original `SESSION_ID` securely outside the repository and be prepared to reapply it or pair again. Reliable durable local state requires persistent storage, which is not included in the Free plan.
- Health checks report service health; they do not keep the service awake.
- Never share `SESSION_ID`, QR/pairing codes, or screenshots containing secrets.
- Do not use unsolicited bulk messaging, automated spam, or activity violating WhatsApp or Render terms. Private mode reduces exposure but cannot guarantee against platform enforcement.

## Memory tuning
Node's old-space heap is capped at 320 MB with a smaller young generation to reduce pressure on a 512 MB instance. Heavy media/AI commands can still exceed the free instance's total memory budget; avoid heavy jobs if Render reports out-of-memory restarts.

## Deployment test
1. Deploy and inspect `/health`. Before authentication, disconnected/degraded may be expected.
2. Confirm `connected:true` after authentication.
3. Test an owner command from the configured owner account.
4. Test from a different number; it should be ignored in private mode.
5. Check logs for repeated restarts or memory errors.

# TraderB Permanent URLs

> **Read this file on every session start.**
> ALWAYS use these URLs when sharing links with Haim.
> NEVER use Cloudflare tunnel URLs (*.trycloudflare.com) or raw port URLs (:8080, :9444, etc.)

## Services

| Service | URL | Notes |
|---------|-----|-------|
| **Dashboard** | https://traderb.duckdns.org/ | Trading bot control, trades, P&L, secrets management |
| **Observatory** | https://traderb.duckdns.org/observatory/ | Agent monitoring, files, activity feed |
| **Observatory (auth)** | https://traderb.duckdns.org/observatory/auth/{TUNNEL_KEY} | Direct auth link — get TUNNEL_KEY from .env |
| **Project Board** | https://traderb.duckdns.org/pm/ | Vikunja — tasks, milestones, kanban |
| **OpenClaw UI** | https://traderb.duckdns.org/openclaw/ | Agent management, gateway control |
| **Wiki (Syncthing GUI)** | https://traderb.duckdns.org/syncthing/ | Manage the Obsidian vault sync device + folder pairing. Credentials in `/root/.syncthing-info/gui-credentials.txt` on VPS. |
| **Vault (on disk)** | /opt/traderb-wiki | Source of truth for the knowledge wiki. OpenClaw memory-wiki plugin owns this directory. Synced to Haim's laptop via Syncthing. |

## API Endpoints (internal only — never expose)
- Dashboard API: https://127.0.0.1:8080/api/
- Observatory API: https://127.0.0.1:9444/api/
- Vikunja API: http://127.0.0.1:3456/api/v1/
- OpenClaw Gateway: http://127.0.0.1:18789/

## Infrastructure
- All services behind nginx reverse proxy with Let's Encrypt SSL
- UFW firewall: only ports 22, 80, 443 open
- Services bind to 127.0.0.1 only — nginx handles external access

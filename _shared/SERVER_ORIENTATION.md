# Server Orientation - TraderB VPS Crew

> Read this on every session start. It tells you where you are and how to work.

## Environment
- Host: Hostinger KVM 2 VPS (srv1074241.hstgr.cloud)
- OS: Ubuntu 24.04 LTS
- OpenClaw: v2026.4.10, gateway on port 18789 (loopback only)
- Operator: Haim Katav (@HaimKatav on GitHub, Telegram ID 808381623)

## File Paths
- TraderB repo: /opt/traderb
- Your workspace: /home/openclaw/.openclaw/workspaces/<your-agent-id>/
- Shared docs: /home/openclaw/.openclaw/workspaces/_shared/
- OpenClaw config: /home/openclaw/.openclaw/openclaw.json

## Git Workflow
Repo at /opt/traderb is cloned via SSH deploy key (read/write).
Branch: refactor/m4-parity-unify-tick (commit 22b4537)
Use conventional commits: type(scope): description
Types: feat, fix, refactor, test, docs, chore

## Task Management (gh CLI - authenticated)
- gh issue list                    # list open issues
- gh issue create --title "..."    # create new
- gh issue close <number>          # close when done
- gh issue comment <number> --body # add progress

## Communication
- To Haim: route through main agent (Telegram notification)
- To another agent: openclaw agent --agent <id> --message "..."
- STOP-AND-ASK: real money, parity, data integrity, architecture

## Safety Rules
1. Never commit secrets/keys to git
2. Never run destructive commands without Haim's approval
3. Never make financial/architectural decisions autonomously
4. Run tests before pushing
5. If unsure, ask - don't guess


## Infrastructure — DO NOT TOUCH

The following services are managed by systemd and nginx. Do NOT create Cloudflare tunnels, do NOT bind to 0.0.0.0, do NOT open firewall ports. Everything is already configured.

### Systemd Services (managed by root, not you)
- `openclaw-gateway.service` — OpenClaw gateway on 127.0.0.1:18789
- `traderb-dashboard.service` — Trading bot dashboard on 127.0.0.1:8080 (HTTPS)
- `traderb-observatory.service — Agent Observatory on 127.0.0.1:9444 (HTTPS)
- Docker: `vikunja` container on 127.0.0.1:3456

### Nginx Reverse Proxy (port 443, Let's Encrypt SSL)
All services are exposed via `https://traderb.duckdns.org/`:
- `/dashboard/` → trading bot (port 8080)
- `/observatory/` → observatory (port 9444)
- `/pm/` → Vikunja (port 3456)
- `/openclaw/` → OpenClaw UI (port 18789)

### Rules
1. NEVER create Cloudflare tunnels — nginx handles everything
2. NEVER share `*.trycloudflare.com` URLs — they are dead
3. NEVER share raw port URLs (e.g., `:9443`, `:8080`) — use nginx paths
4. Always read `URLS.md` before sharing any link with Haim
5. If a service needs restarting, tell Haim to run the systemctl command — you cannot do it yourself

## Multi-Agent Architecture

This project runs 14 real OpenClaw agents, not personas. Each agent is an independent Claude session with its own workspace, memory, and identity.

### Your Agent Identity
- Your workspace: `~/.openclaw/workspaces/<your-agent-id>/`
- Your SOUL.md defines your role and capabilities
- Your memory: `~/.openclaw/workspaces/<your-agent-id>/memory/`
- Shared docs: `~/.openclaw/workspaces/_shared/` (TEAM.md, URLS.md, this file)

### Inter-Agent Communication
- To send work to another agent: `openclaw agent --agent <agent-id> --message "..."`
- To check what other agents did: read their workspace `memory/` logs
- The main router receives all Telegram messages and spawns the right agents

### File Sharing Convention
- Save deliverables to your workspace directory
- Use clear, dated filenames: `2026-04-12_ux_review_dashboard.md`
- Shared deliverables go to `_shared/`
- Files are visible via Observatory Files tab

### Daily Activity Logging
Every session, write a log to `memory/YYYY-MM-DD.md`:
```
- HH:MM — What you did | files: path/to/changed.py
```

## Access Control

### Allowed without asking (read-only)
- Reading any file in the repo or workspaces
- Running tests (`make test`, `make regression`)
- Browsing logs, checking service status
- Writing to your own workspace and memory

### Requires Haim's approval via Telegram
- Restarting any systemd service
- Modifying `.env` or secrets
- Deploying code (git push, Docker operations)
- Creating or modifying nginx config
- Any action involving real money or live trading
- Architectural decisions or major refactors

### Never allowed
- Binding services to 0.0.0.0
- Opening firewall ports
- Creating Cloudflare tunnels
- Committing secrets to git
- Making financial decisions autonomously

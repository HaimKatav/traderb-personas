# Server Orientation — Bootstrap Summary

> Full reference: `../_shared/SERVER_ORIENTATION.md` (read for complete service list, access control details, logging format)

## Environment
Host: Hostinger KVM 2 (srv1074241.hstgr.cloud) | Ubuntu 24.04 | OpenClaw v2026.4.10

## File Paths
- Repo: `/opt/traderb`
- Your workspace: `/home/openclaw/.openclaw/workspaces/<your-agent-id>/`
- Shared docs: `/home/openclaw/.openclaw/workspaces/_shared/`

## Git
Branch: check with `git branch --show-current` | Conventional commits: `type(scope): description`

## Tasks (gh CLI)
`gh issue list` | `gh issue create` | `gh issue close <n>`

## Communication
To Haim: route through main agent. To another agent: `openclaw agent --agent <id> --message "..."`
STOP-AND-ASK: real money, parity, data integrity, architecture.

## Safety Rules
1. Never commit secrets to git
2. Never run destructive commands without Haim's approval
3. Never make financial/architectural decisions autonomously
4. Run tests before pushing
5. If unsure, ask

## Infrastructure — DO NOT TOUCH
All services managed by systemd + nginx. NEVER create Cloudflare tunnels, bind to 0.0.0.0, or open firewall ports.
All URLs via `https://traderb.duckdns.org/` — `/dashboard/`, `/observatory/`, `/pm/`, `/openclaw/`
Read `URLS.md` before sharing any link.

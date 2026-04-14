# Vikunja Task Management — Agent Guide

## Your Personal Token
Each agent has its own Vikunja API token at: `vikunja-token.txt` (in your workspace root).

## Quick Reference
- **API URL**: `http://127.0.0.1:3456/api/v1`
- **Auth header**: `Authorization: Bearer $(cat ~/vikunja-token.txt)` (read from your workspace)
- **Web UI**: `https://traderb.duckdns.org/pm/`

## Common Operations

List your tasks:
```bash
VK_TOKEN=$(cat /home/openclaw/.openclaw/workspaces/<your-agent-id>/vikunja-token.txt)
curl -s http://127.0.0.1:3456/api/v1/tasks/all -H "Authorization: Bearer $VK_TOKEN"
```

Create a task in project 4 (TraderB Development):
```bash
curl -s -X PUT http://127.0.0.1:3456/api/v1/projects/4/tasks \
  -H "Authorization: Bearer $VK_TOKEN" \
  -H 'Content-Type: application/json' \
  -d '{"title":"[your-agent-id] Task description","labels":[]}'
```

Mark task done:
```bash
curl -s -X POST http://127.0.0.1:3456/api/v1/tasks/<task_id> \
  -H "Authorization: Bearer $VK_TOKEN" \
  -H 'Content-Type: application/json' \
  -d '{"done":true}'
```

## Rules
1. Create a Vikunja task BEFORE starting any new work
2. Use title format: `[your-agent-id] Description`
3. Update task status when work starts, progresses, or completes
4. Use labels: Backend=7, Frontend=8, Infrastructure=9, Testing=10, Security=11, Documentation=12, Data=13, UX=14

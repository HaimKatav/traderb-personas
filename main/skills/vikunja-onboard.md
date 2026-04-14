# Vikunja Onboarding — Agent Initialization

When Haim says "onboard agents to vikunja", "set up vikunja for agents", "agents start using vikunja", or "log past work to vikunja", follow these steps:

## Step 1: Verify Connectivity

```bash
VK_TOKEN=$(cat ~/.openclaw/workspaces/_shared/vikunja-api-token.txt)
curl -s http://localhost:3456/api/v1/projects/4 -H "Authorization: Bearer $VK_TOKEN" | python3 -c "import json,sys; d=json.load(sys.stdin); print(f\"Connected to project: {d[\"title\"]}\")"
```

## Step 2: Dispatch Each Agent

Send a message to each agent telling them to:
1. Read `~/.openclaw/workspaces/_shared/vikunja-skill.md`
2. Create tasks for ALL work they have already completed (mark as done)
3. Create any in-progress or planned tasks
4. Confirm back when done

Use this dispatch template for each agent:

```
@<agent-name> Read ~/.openclaw/workspaces/_shared/vikunja-skill.md and then:
1. Log all your past completed work as done tasks in Vikunja project 4
2. Use title format: [<agent-name>] Description
3. Add appropriate labels (Backend=7, Frontend=8, Infrastructure=9, Testing=10, Security=11, Documentation=12, Data=13, UX=14)
4. For each task, add a brief description of what was done
5. From now on, create a Vikunja task BEFORE starting any new work
Report back when finished.
```

## Step 3: Confirm

After dispatching, tell Haim: "All agents have been instructed to log their work to Vikunja. Check the Project Management board at https://traderb.duckdns.org/pm/ to see tasks appearing."

## Agent List

main, project-lead, strategy-architect, python-dev, frontend-dev, data-engineer, qa-tester, devops, security-specialist, risk-manager, compliance-officer, logging-specialist, ux-designer, transparency-agent

# TraderB Development Crew

> Owner: **Haim Katav** (HK) — Final authority on all decisions.
> Bot: **@TraderCEO_bot** on Telegram

## The Team

| Name | Role | Agent ID | What They Do |
|------|------|----------|-------------|
| **Ari** | Project Lead | `project-lead` | Runs sprints, reviews architecture, coordinates the crew. The one who decides task order and signs off before anything reaches Haim. |
| **Noa** | Strategy Architect | `strategy-architect` | Designs trading logic, researches prop firm rules, backtests strategies. Thinks in edge-cases and market scenarios. |
| **Lior** | Python Developer | `python-dev` | Hands-on builder. Writes the core bot logic, adapters, tests. Pragmatic, readable code, ships fast. |
| **Maya** | Frontend Developer | `frontend-dev` | Builds the dashboard and debug browser UI. HTML/CSS/JS, real-time displays, charts. |
| **Dan** | Data Engineer | `data-engineer` | Owns data pipelines, feed integrity, schema validation. If data flows through it, Dan built it. |
| **Yael** | QA Tester | `qa-tester` | Breaks things on purpose. Manual testing, bug reports, regression suites. Nothing ships without Yael's green light. |
| **Eitan** | DevOps Engineer | `devops` | Runs the KVM 2 VPS, Docker, deployment, monitoring, CI/CD. Keeps the lights on. |
| **Tamar** | Security Specialist | `security-specialist` | Auth, secrets management, network hardening, prompt injection defense. Paranoid by design. |
| **Omer** | Risk Manager | `risk-manager` | Market risk, stress testing, drawdown limits, edge-case scenarios. The one who says "what if it all goes wrong." |
| **Shira** | Compliance Officer | `compliance-officer` | Prop firm rules, evaluation criteria, regulatory compliance. Makes sure we don't break the rules. |
| **Gal** | Logging Specialist | `logging-specialist` | Audit trails, event structure, log formats. Every decision the bot makes has a paper trail thanks to Gal. |
| **Rotem** | UX Designer | `ux-designer` | UI layout, visual hierarchy, operator experience. Makes the dashboard something a trader can actually use under pressure. |
| **Amit** | Transparency Agent | `transparency-agent` | Decision explainability, operator visibility. Makes sure Haim can always see WHY the bot did what it did. |
| **Main** | Router | `main` | The traffic controller. Receives all messages, routes to the right specialist. Never does the work — just knows who should. |

## How to Talk to the Team

From Telegram, just tell @TraderCEO_bot what you need. Main (the router) will figure out who handles it.

Examples:
- "Fix the trailing stop bug" → Routed to **Lior** (python-dev)
- "Is the dashboard showing stale data?" → Routed to **Maya** (frontend-dev) + **Dan** (data-engineer)
- "Are we compliant with FTMO rules?" → Routed to **Shira** (compliance-officer)
- "Deploy the latest build" → Routed to **Eitan** (devops)
- "Review the M4 milestone" → Routed to **Ari** (project-lead)

## Quick Reference

**When you need code written**: Lior (python-dev) or Maya (frontend-dev)
**When something breaks**: Yael (qa-tester) investigates, Lior/Maya fix
**When you need a decision reviewed**: Ari (project-lead) coordinates
**When money is at risk**: Omer (risk-manager) + Shira (compliance-officer)
**When security is involved**: Tamar (security-specialist)
**When infrastructure needs work**: Eitan (devops)

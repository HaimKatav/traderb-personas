# Governance — Bootstrap Summary

> Full reference: `openclaw/GOVERNANCE.md` (read when planning sprints, reviewing milestones, or resolving process questions)

## CEO Model
Haim gives high-level directives. Team autonomously executes. No micromanagement.
When Haim says "I want X": Project Lead breaks into tasks with DOD → assigns personas → posts plan → work starts ONLY after Haim approves.

## Definition of Done (every task)
- Code written, self-validated, pushed to feature branch
- Unit tests passing (`make test`), regression passing (`make regression`) if trading logic touched
- Reviewed by required reviewers (see review chain)
- Merged to `develop` (NOT `main` — main is release only)
- Docs updated, logging verified

## Review Chain
| Change touches | Required reviewers |
|---|---|
| Trading logic, signals | Risk Manager, Compliance Officer |
| Position sizing, stops | Risk Manager |
| Prop firm rules | Compliance Officer |
| Auth, secrets, deps | Security Specialist |
| Broker, data pipeline | Data Engineer |
| Architecture, patterns | Project Lead |
| UI, dashboard | UX Designer |
| Any change | Project Lead (always) |

## Git Discipline
- Conventional commits: `feat:`, `fix:`, `refactor:`, `docs:`, `test:`, `chore:`
- Feature branches: `feature/<ticket>-<short-description>`
- `develop` = integration (stable). `main` = release only (never broken).
- No work outside Git. If it's not committed, it didn't happen.

## Coordination
- All inter-agent work goes through main router
- Use file handoffs: `openclaw/handoffs/<from>-to-<to>_<date>_<topic>.md`
- Project Lead tracks all coordination
- Tasks live in **Vikunja** (https://traderb.duckdns.org/pm/) — single source of truth
- `openclaw/SPRINT_PLAN.md` is a read-only sprint summary, not for task tracking

## Tool Failure
Document → commit WIP → notify Haim via Telegram → wait for direction. No work lost.

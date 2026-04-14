# QA Templates

## Critical Test Scenarios

| Scenario | What to check |
|----------|--------------|
| Bot startup | Config loads, broker connects, session detected, events logged |
| Signal generation | Zone → signal → event visible in UI and logs |
| Trade entry | Order → fill → position shown → P&L updating |
| Trade exit (target) | Target hit → closed → P&L correct → logged |
| Trade exit (stop) | Stop hit → slippage within tolerance → loss correct |
| Daily loss limit | Bot stops trading when cap reached |
| Prop rule violation | Bot flattens and stops |
| Session boundary | Flattens at EOD, no trades outside window |
| API disconnect | Graceful handling, no orphaned positions |
| Config change | Hot-reload works, new params next tick |
| Replay parity | Same signals on identical data (parity check) |
| Contract rollover | New symbol accepted, old symbol rejected |

## Bug Report Format
```
Title: [Short description]
Severity: Critical|High|Medium|Low
Steps: 1. ... 2. ... 3. ...
Expected: [What should happen]
Actual: [What actually happens]
Environment: [Browser, Python version, mode]
Logs: [Relevant entries]
Screenshots: [If applicable]
```

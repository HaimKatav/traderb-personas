# UX/UI Review — All Pages (2026-04-11)

## Dashboard (https://traderb.duckdns.org/)

**Status**: Working locally on port 8080. Awaiting nginx reload to serve at root.

### Findings
- [FIXED] Stats bar (10 columns) overflowed on mobile — added `flex-wrap` breakpoint at 900px
- [FIXED] Header wrapped awkwardly on small screens — added `flex-wrap` + gap
- [OK] Dark theme consistent with CSS variables (--bg, --accent, etc.)
- [OK] Config grid collapses to single column on mobile (<900px)
- [OK] Secrets tab: form wraps correctly, masked values display, delete confirms
- [OK] Trade cards, log entries, suite management all use consistent styling
- [OK] Viewport meta tag present, no horizontal overflow issues after fix

### Secrets Tab (new)
- Add form: auto-uppercases key names, flex-wrap for mobile
- List: accent-colored key names, masked values, red delete buttons
- Auth: requires X-TraderB-Token (fetched via /api/bootstrap)
- Tested: 10 secrets load correctly with proper masking

## Observatory (https://traderb.duckdns.org/observatory/)

**Status**: Running on port 9444 via systemd. Auth gate working.

### Findings
- [FIXED] API calls used absolute paths (/api/agents) — broke under /observatory/ prefix. Added _basePath auto-detection.
- [FIXED] Auth redirect went to / (dashboard) instead of /observatory/. Now redirects correctly.
- [FIXED] Session cookie name was "session" (same as dashboard) — renamed to "obs_session" to prevent conflicts.
- [OK] Mobile viewport set with `maximum-scale=1.0, user-scalable=no` (good for mobile app feel)
- [OK] Responsive grid at 520px and 768px breakpoints
- [OK] Tabs: Feed, Files, Board all render (14 agents, 15 file agents, 3 tasks)
- [OK] Dark theme consistent (different palette from dashboard, both polished)

## Vikunja (https://traderb.duckdns.org/pm/)

**Status**: Running in Docker on port 3456. HTML returns correctly.

### Findings
- [NOTE] Assets reference `/assets/...` — nginx sub_filter rewrites to `/pm/assets/` (needs reload to activate)
- [OK] PUBLICURL configured as `https://traderb.duckdns.org/pm/`
- [OK] 7 tasks created, 6 labels configured
- [PENDING] Full visual test after nginx reload — SPA routing may need verification

## OpenClaw (https://traderb.duckdns.org/openclaw/)

**Status**: Active on port 18789 via systemd.

### Findings
- [OK] HTML loads correctly with dark theme, favicon, PWA manifest
- [OK] Proxy passthrough working (HTTP, no SSL needed for loopback)
- [OK] Responsive viewport meta present

## Summary
- 3 bugs fixed (observatory paths, auth redirect, cookie collision)
- 1 mobile improvement (dashboard stats bar wrapping)
- All pages serve locally. Nginx reload needed to activate new routing.

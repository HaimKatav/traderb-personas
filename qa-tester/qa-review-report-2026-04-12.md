# QA Review Report — TraderB Application
**Date:** 2026-04-12  
**Reviewer:** Maya (QA Tester)  
**Branch:** refactor/m4-parity-unify-tick (commit 22b4537)  
**Scope:** Static code analysis of `dashboard.html`, `observatory.html`, and `run_server.py`

---

## Summary

| Severity | Count |
|----------|-------|
| Critical | 2 |
| High     | 2 |
| Medium   | 2 |
| Low      | 1 |

---

## Critical Bugs

### BUG-001 — Observatory: `_basePath` IIFE never closed (JS syntax/logic error)

**File:** `observatory.html` lines 532–738  
**Severity:** Critical  
**Category:** Functionality / JavaScript Logic

**Description:**  
The arrow function IIFE used to compute `_basePath` is opened at line 533 but never properly closed before defining the Settings-related functions. The structure is:

```javascript
const _basePath = (() => {
    const p = window.location.pathname;
    const m = p.match(/^(\/observatory)\b/);
    return m ? m[1] : '';   // ← returns here, but function body is never closed
  
  // ← loadGatewayStatus, loadModelSettings, loadSessionList, etc. all land here
  // ← window.loadGatewayStatus = loadGatewayStatus; (dead code)
  ...
})();  // ← this closes the _basePath IIFE, not the outer IIFE
```

**Impact:**
- The `return m ? m[1] : '';` at line 537 causes the `_basePath` IIFE to exit early. All code below it (lines 540–737) is dead code — it will never execute.
- The `window.loadGatewayStatus = loadGatewayStatus;` and sibling `window.*` assignments at lines 720–727 **never run**. These functions are never exposed globally.
- The Settings tab's Refresh button (`onclick="loadGatewayStatus()"`) and "Reset All Sessions" button (`onclick="resetAllSessions()"`) will throw `ReferenceError` when clicked — the functions don't exist in global scope.
- `_settingsLoaded` state and the `loadSettings()` hook for the Settings tab click event (lines 709–717) are also dead code.

**Steps to Reproduce:**
1. Open Observatory at `https://traderb.duckdns.org/observatory/auth/<key>`
2. Click the **Settings** tab
3. Click the **Refresh** button next to "Gateway: checking..."
4. Open the browser console → observe `Uncaught ReferenceError: loadGatewayStatus is not defined`

**Expected:** Gateway status refreshes.  
**Actual:** Console error, button does nothing.

**Fix:** Close the `_basePath` IIFE properly at line 537:

```javascript
const _basePath = (() => {
    const p = window.location.pathname;
    const m = p.match(/^(\/observatory)\b/);
    return m ? m[1] : '';
})();  // ← add this closing line
```

Then move `loadGatewayStatus`, `loadModelSettings`, `loadSessionList`, the `window.*` assignments, and `loadSettings` to run OUTSIDE the `_basePath` IIFE, within the outer IIFE scope.

---

### BUG-002 — Observatory: Settings panel rendered outside `.content` div

**File:** `observatory.html` lines 428–457  
**Severity:** Critical  
**Category:** Visual / Layout

**Description:**  
The `<div id="panel-settings">` element is placed **outside** the `<div class="content">` container. The `.content` div closes at line 428, but `panel-settings` starts at line 431.

```html
  <div id="panel-feed" class="panel" ...></div>
</div>         ← .content closes here (line 428)


  <div id="panel-settings" class="panel" ...>   ← Settings is OUTSIDE .content
```

**Impact:**
- The Settings panel does not inherit `.content` padding (`padding: 12px 16px 80px`)
- Its content renders edge-to-edge with no horizontal padding — visually broken on mobile
- Depending on browser layout, it may appear below the modal overlay, or partially behind it

**Steps to Reproduce:**
1. Open Observatory
2. Click the **Settings** tab
3. Observe the Agent Models and Session Management sections have no left/right padding — text is flush with the screen edge

**Fix:** Move `<div id="panel-settings">` inside the `.content` div, alongside the other panels.

---

## High Severity

### BUG-003 — Dashboard: Stale broker label "ALPACA"

**File:** `dashboard.html` line 567  
**Severity:** High  
**Category:** Data Integrity

**Description:**  
The dashboard subtitle hardcodes "ALPACA" as the broker name:

```javascript
document.getElementById('subLabel').textContent =
    `v2.0 · ALPACA · ${mode}` + (data.running ? '' : ' · STOPPED');
```

The project pivoted from Alpaca to **Tradovate/TopstepX** for NQ/MNQ futures on 2026-04-11. The label now shows outdated, incorrect broker information to the user.

Additionally, the initial static HTML at line 295 also shows `ALPACA`:
```html
<span id="subLabel" class="sub">v2.0 · ALPACA · PAPER</span>
```

**Impact:**  
Misleading — Haim would see "ALPACA" as the broker during live trading on Tradovate. Could cause confusion when reading the dashboard from a mobile device.

**Fix:**  
- Update the static label to `v2.0 · TRADOVATE · PAPER`
- Update the JS to use `data.broker || 'TRADOVATE'` if the API returns broker info, or hardcode `TRADOVATE` until the API serves it

---

### BUG-004 — Dashboard: `--muted` CSS variable is undefined

**File:** `dashboard.html` lines 323, 591, 603, 606, 630  
**Severity:** High  
**Category:** Visual / Dark Theme

**Description:**  
The variable `--muted` is referenced 5 times in JavaScript but never declared in the CSS `:root` block. The declared variables are `--text-dim`, `--text-label`, etc. — there is no `--muted`.

Affected elements:
- `sGap` stat bar cell (line 323) — initial style attribute
- `goalBadge` color (line 591) — goal active state
- `sHtfBias` color (line 603, 606) — MTF off and neutral states
- `sGap` color (line 630) — no-gap state

**Impact:**  
When `var(--muted)` is unresolved, the browser falls back to the inherited color or transparent, meaning these UI elements show up in the **wrong color** (likely white/default text instead of the intended muted gray). The neutral/inactive states for Gap, HTF Bias, and Daily Goal are visually indistinguishable from important/active states.

**Steps to Reproduce:**
1. Open the dashboard
2. Look at the Stats Bar — the **Gap** stat shows `—` in white instead of the intended dim gray
3. Start the bot and wait for a non-trending market — **HTF Bias** will show `NEUTRAL` in white
4. Open browser DevTools → inspect the element → `--muted` resolves to nothing

**Fix:**  
Either define the variable in `:root`:
```css
:root {
    /* ...existing vars... */
    --muted: #475569;  /* same as the text-dim-ish colors used elsewhere */
}
```
Or replace all `var(--muted)` occurrences with `var(--text-dim)`.

---

## Medium Severity

### BUG-005 — Observatory: File browser breadcrumb `onclick` breaks in global scope

**File:** `observatory.html` lines 932–934  
**Severity:** Medium  
**Category:** Functionality

**Description:**  
The file browser breadcrumb generates HTML with inline `onclick` attributes that reference `_filePath` and `loadFileList()` as global variables:

```javascript
var bcHtml = '<span onclick="_filePath=[];loadFileList()">' + _fileAgent + '</span>';
// Also:
'<span onclick="_filePath=_filePath.slice(0,' + (i+1) + ');loadFileList()">'
```

Both `_filePath` (declared `let` at line 904) and `loadFileList` (function at line 924) are scoped to the outer IIFE — they are not on `window`. When a user clicks a breadcrumb item, the browser executes the `onclick` in global scope, resulting in:
- `_filePath` read as `undefined` (ReferenceError in strict mode, or a new global `undefined` assignment)
- `loadFileList` not found (ReferenceError)

Note: `window._filePath = _filePath;` and `window.loadFileList = loadFileList;` at lines 720–721 are **dead code** (see BUG-001), so these are never exposed globally.

**Steps to Reproduce:**
1. Open Observatory → Files tab
2. Select an agent and navigate into a subfolder
3. Click a breadcrumb segment to navigate back up
4. Observe: browser console shows ReferenceError

**Fix:**  
After fixing BUG-001, ensure `_filePath` and `loadFileList` are properly exposed:
```javascript
window._filePath = _filePath;
window.loadFileList = loadFileList;
```
These should be in an always-executing code path (not after a `return`).

---

### BUG-006 — Dashboard: Open trade progress bar is non-functional

**File:** `dashboard.html` lines 699–706  
**Severity:** Medium  
**Category:** Data Integrity / Functionality

**Description:**  
The progress bar shown on open trade cards calculates its width using a static heuristic rather than real price data:

```javascript
style="width:${Math.max(0, Math.min(100, (t.pnl > 0 ? 50 : 10)))}%"
```

This means every profitable open trade always shows 50% progress, and every losing trade shows 10% — regardless of actual price movement relative to stop/target.

There is even a code comment acknowledging this:
```javascript
// We don't have current_price in the trade, but we can estimate from pnl
```

**Impact:**  
The progress bar gives Haim a false sense of how far price has moved toward the target. A trade at 99% of target would show 50% just like one at 1% of target.

**Fix:**  
Include `current_price` in the trade object returned by `/api/status`, then compute actual progress as:
```
progress = (current_price - entry_price) / (take_profit - entry_price) * 100
```
(with sign adjustment for short trades)

---

## Low Severity

### BUG-007 — Dashboard: `subLabel` always shows "v2.0" hardcoded version

**File:** `dashboard.html` line 567  
**Severity:** Low  
**Category:** Data Integrity

**Description:**  
The version shown in the header sub-label is hardcoded as `v2.0` and is not populated from any API field. The bot is now post-M4.

**Fix:**  
Either derive the version from `data.version` if the API provides it, or update the string to match the current milestone (`v4.0` or similar).

---

## Positive Findings

- **Dark theme is consistently applied** across both apps. CSS variables are used correctly throughout (aside from the `--muted` issue above), and no unstyled elements or light-theme leakage was observed in the HTML source.
- **Auth security model** is correctly implemented — the `X-TraderB-Token` monkey-patch in `dashboard.html` and the `@require_auth` decorator in `run_server.py` cover all write endpoints.
- **XSS prevention** is in place: `escHtml()` is used for user-visible log messages (line 770), and trade data is mostly rendered via `.toFixed()` and `.textContent` patterns.
- **Mobile-responsive breakpoints** exist in both `dashboard.html` (line 239) and `observatory.html` (lines 281–288).
- **Observatory modal** (agent detail slide-up) has a clean animation and tap-to-dismiss works correctly from HTML inspection.
- **Observatory auto-refresh** at 15s interval (line 895) refreshes only the active tab, which is efficient.
- **Connection banner** in dashboard correctly shows/hides on polling failure.

---

## Recommended Fix Priority

1. **BUG-001** — Fix `_basePath` IIFE closure (unblocks Settings tab entirely)
2. **BUG-002** — Move Settings panel inside `.content` (1-line HTML fix)
3. **BUG-004** — Define `--muted` CSS var (5-second fix, affects visual correctness)
4. **BUG-003** — Update broker label from ALPACA → TRADOVATE (data integrity for Haim)
5. **BUG-005** — Expose file browser functions to `window` (depends on BUG-001 fix)
6. **BUG-006** — Proper trade progress bar (requires API change)
7. **BUG-007** — Update version string

---

*Report generated by Maya (QA Tester subagent) via static code analysis of the TraderB repository.*  
*Activity log: 2026-04-12 — QA review of dashboard.html and observatory.html | files: observatory.html, dashboard.html, run_server.py*

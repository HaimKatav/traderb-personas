# TraderB Dashboard — UX Review Report
**Reviewer:** Rotem (UX Designer)
**Date:** 2026-04-12
**URL reviewed:** https://traderb.duckdns.org/
**Source reviewed:** `/opt/traderb/dashboard.html`
**Scope:** Full visual + UX audit — design, data display, mobile responsiveness, broken elements

---

## Executive Summary

The TraderB dashboard has a strong, professional foundation. The dark navy color palette, dual-font system (Space Grotesk + JetBrains Mono), and semantic color coding are all solid choices for a trading application. However, there are several significant issues: a missing CSS variable causing invisible UI elements, outdated branding from the Alpaca pivot, a broken progress bar, poor mobile handling for the tab row and trade grid, and multiple instances of browser-native `alert()`/`confirm()` dialogs that undermine the polished visual treatment.

**Severity legend:** 🔴 Critical · 🟠 High · 🟡 Medium · 🟢 Low

---

## 1. Visual Design

### 1.1 Color Palette

The palette is cohesive and purpose-built for a dark trading terminal:

| Variable | Value | Use |
|----------|-------|-----|
| `--bg` | `#020917` | Page background |
| `--bg-card` | `#0a1628` | Card surfaces |
| `--bg-input` | `#0f172a` | Input/card inner surfaces |
| `--border` | `#1e3a5f` | Primary border |
| `--border-dim` | `#0f2a45` | Subtle separators |
| `--text` | `#e2e8f0` | Primary text |
| `--text-dim` | `#64748b` | Secondary text |
| `--text-label` | `#94a3b8` | Labels |
| `--accent` | `#38bdf8` | Sky blue highlights |
| `--green` | `#22c55e` | Positive/long |
| `--red` | `#ef4444` | Negative/short/stop |
| `--yellow` | `#fbbf24` | Warnings/pending |

**Assessment:** The palette is well-chosen. The near-black background (`#020917`) with sky-blue accent creates a premium trading terminal aesthetic. Semantic use of green/red/yellow is consistent and intuitive.

**Issue 🔴:** `--muted` is referenced in multiple places (JS and one HTML inline style) but is **never defined** in `:root`. Affected elements will inherit the default text color unexpectedly. See Section 4.1 for details.

### 1.2 Typography

- **Space Grotesk** (400, 600, 700) for UI chrome — clean, modern, legible
- **JetBrains Mono** (400, 700) for all data/numbers — excellent choice for a trading app; monospaced numbers prevent jitter in live updates

Font sizes range from 9px (stat labels, trade field labels) to 20px (brand). This is quite wide. The 9px labels may fail accessibility contrast/legibility checks on lower-res screens.

**Issue 🟡:** Labels at `9px` (`.stat-label`, `.trade-field-label`) are below the 11px practical minimum for most users. Consider bumping to 10–11px.

### 1.3 Overall Layout & Chrome

- **Header:** Sticky with `backdrop-filter: blur(8px)` — nice layering feel. Well-structured with brand left, P&L + controls right.
- **Stats Bar:** Full-width strip below header with 10 discrete stat tiles — clean scanability at a glance. Good use of right-border separators.
- **Tab Bar:** Clean with active state underline. The "Debug Timeline" external link with `margin-left:auto` is visually separated from the main tabs — appropriate treatment.
- **Content Area:** `max-width: 1200px; margin: 0 auto` with 24px padding — good reading width on desktop.
- **Cards:** 10px border radius, `#1e3a5f` border, subtle `#0f172a` background — consistent throughout.
- **Scrollbar:** Custom 4px scrollbar styled to match theme — polished detail.

### 1.4 Branding / Stale Text 🔴

The header brand text and sub-label are hardcoded as:
```
"NASDAQ Bot"
"v2.0 · ALPACA · PAPER"
```

The project has pivoted to **Tradovate / TopstepX for NQ/MNQ futures** (as of 2026-04-11). Both the static brand `<span>` and the JS-rendered sub-label string (line 567: `` `v2.0 · ALPACA · ${mode}` ``) reference Alpaca. This is misleading and needs to be updated.

**Fix needed:** Update brand to "NQ/MNQ Bot" or "TraderB" and sub-label to reference Tradovate/TopstepX.

---

## 2. Data Display

### 2.1 Trade Cards

Trade cards display Entry / Stop / Target / Trail in a 4-column grid. The color coding is semantically appropriate:
- Entry → `var(--text-label)` (neutral)
- Stop → `var(--red)`
- Target → `var(--green)`
- Trail → `var(--yellow)`

The P&L value at 18px JetBrains Mono bold with pos/neg color is immediately readable. Trade direction badges (LONG/SHORT/PENDING) with colored background tints are clear.

**Issue 🟠 — Progress Bar is Placeholder Logic:**
The progress bar for open trades uses:
```js
style="width:${Math.max(0, Math.min(100, (t.pnl > 0 ? 50 : 10)))}%"
```
This renders either a static 50% bar (if P&L > 0) or a static 10% bar (if P&L ≤ 0). It does not represent actual trade progress toward the target. This is misleading — users could interpret it as meaningful data.

**Fix:** Either remove the progress bar until real data (current price, distance to target) is available from the API, or replace with a simple textual R:R status. A fake progress indicator is worse than no indicator.

### 2.2 Stats Bar

Ten stat tiles cover: Symbol, Timeframe, Market, Open Trades, Risk/Trade, Daily Limit, Min R:R, Ticks, HTF Bias, Gap.

Good: Market status shows green/red with time remaining. HTF Bias shows direction with color.

**Issue 🔴 — `var(--muted)` in Stats Bar HTML:**
The Gap stat has a hardcoded inline style:
```html
<div id="sGap" class="stat-value" style="color:var(--muted)">—</div>
```
Since `--muted` is undefined, the initial "—" renders with no explicit color. Same issue in JS for Gap and HTF Bias neutral state.

### 2.3 Log Display

- JetBrains Mono at 12px — appropriate for log output
- Flex row with time / level / message — clear visual hierarchy
- Color-coded levels: WARN=yellow, ERROR=red, INFO=`--text-dim`
- `max-height: 600px; overflow-y: auto` — correct

**Issue 🟡:** Log levels INFO render in `--text-dim` (#64748b) — this is quite muted. On a dark background, INFO messages may be hard to distinguish from the background. Consider `--text-label` (#94a3b8) for INFO.

### 2.4 Config Tab

Sliders with live value display, toggles, and selects are all well-rendered. The `formatLabel()` function correctly converts `snake_case` to human-readable labels.

**Issue 🟡:** Config fields have no visual unsaved-changes indicator. If a user adjusts 10 sliders, there's no "dirty" state feedback until they hit Save (which triggers a browser `alert()`). Consider highlighting modified fields or showing a floating save bar with a count of pending changes.

### 2.5 Test Suites / Results Table

The results table in the Results sub-tab uses an inline `<table>` styled with JetBrains Mono. The table lacks any `overflow-x: auto` wrapper, which means it will overflow on small screens.

**Issue 🟠:** Wrap the results table in a `<div style="overflow-x:auto">` container to prevent horizontal page blowout on mobile.

---

## 3. Mobile Responsiveness

The dashboard has a single responsive breakpoint at `@media (max-width: 900px)`. Here's what changes:
- Config grid: 3 columns → 1 column ✅
- Stats bar: wraps, stats become `33.3% - 1px` each (3 per row) ✅
- Header: `flex-wrap: wrap; gap: 8px` ✅

### 3.1 Trade Grid — Not Responsive 🟠

```css
.trade-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 12px;
}
```

There is **no mobile override** for `.trade-grid`. On a 360px wide phone, 4 columns = ~75px each. The trade field values (e.g., `21435.50`) at 14px JetBrains Mono bold will be very tight and may clip or wrap awkwardly.

**Fix:**
```css
@media (max-width: 600px) {
  .trade-grid { grid-template-columns: repeat(2, 1fr); }
}
```

### 3.2 Tab Bar — Overflow Risk 🟠

The tab bar contains 5 tab buttons + the "Debug Timeline" link. On a phone (360px), these are 12px font with 20px horizontal padding each. The total width is likely ~400px+ — exceeding the viewport. There is no `overflow-x: auto` or scroll behavior on the `.tabs` container.

**Fix:**
```css
.tabs {
  overflow-x: auto;
  -webkit-overflow-scrolling: touch;
  scrollbar-width: none;
}
.tabs::-webkit-scrollbar { display: none; }
```
This gives a swipeable tab bar rather than clipped/overflowing tabs.

### 3.3 Stats Bar — 10 Items at 33.3% = 4 Rows 🟡

At 33.3% per stat, 10 stats = 3 complete rows + 1 row with 1 item. The lone 10th stat (Gap) will render at full width in the last row, which looks unbalanced.

**Fix:** Either reduce to 9 stats (merge or remove a low-priority one), or use `50%` (2 columns × 5 rows) at mobile for a more balanced grid.

### 3.4 Content Padding on Mobile 🟢

`padding: 24px` on `.content` is generous on desktop but may feel tight combined with card margins on very small screens. Consider `padding: 16px` below 480px.

### 3.5 Trade Header Overflow 🟡

The `.trade-header` is a flex row with `justify-content: space-between`. On very small screens, the left side (badge + symbol + trade ID) and right side (P&L value) may not have enough space, causing the P&L to wrap below or overlap.

**Fix:** Add `flex-wrap: wrap; gap: 8px` to `.trade-header` as a fallback.

---

## 4. Broken Elements

### 4.1 🔴 Missing CSS Variable: `--muted`

**Impact:** UI elements render with incorrect/unexpected color.

`--muted` is referenced in 4 places but never defined in `:root`:

1. **HTML:** `<div id="sGap" class="stat-value" style="color:var(--muted)">—</div>` (line 323)
2. **JS:** `htfEl.style.color = 'var(--muted)'` (neutral HTF bias, line 603)
3. **JS:** `gapEl.style.color = 'var(--muted)'` (no gap, line 629)
4. **JS:** Goal badge: `badge.style.color = 'var(--muted)'` (active but unreached goal, line 591)

**Fix:** Add to `:root`:
```css
--muted: #475569;
```
(This matches the existing muted tone used elsewhere as a literal `#475569`.)

### 4.2 🟠 Browser `alert()` and `confirm()` for User Feedback

All critical user interactions use browser-native dialogs:
- `alert('Config updated!')` — after saving config
- `alert('No changes to save.')` — spurious save attempt
- `alert('Errors:\n' + ...)` — config validation errors
- `alert(res.error)` — start/stop bot errors
- `confirm('Emergency flatten...')` — **FLATTEN** button
- `confirm('Delete suite...')` — suite deletion

These are jarring, unstyled, block the main thread, and are inconsistent with the otherwise polished dark UI. The FLATTEN action is particularly critical — a browser confirm dialog for an emergency operation is not appropriate.

**Fix:** Implement a minimal toast notification system for success/info messages, and a styled confirmation modal for destructive actions (FLATTEN, delete suite). A simple overlay div with the existing CSS variables can achieve this in ~50 lines.

### 4.3 🟡 Sub-label JavaScript: Hardcoded "ALPACA"

```js
document.getElementById('subLabel').textContent =
  `v2.0 · ALPACA · ${mode}` + (data.running ? '' : ' · STOPPED');
```

Since the project has migrated to Tradovate, this string should be updated. It could also be made data-driven from the API status response (e.g., a `broker` field) to avoid this issue recurring.

### 4.4 🟡 Progress Bar Fake Value

Already detailed in Section 2.1. The 50%/10% hardcoded progress bar is functionally broken — it conveys false precision.

### 4.5 🟢 `<a>` Tab-Style Link — Inline Style Block

The "Debug Timeline" link in the tabs bar is implemented with a large inline `style` attribute including `onmouseover`/`onmouseout` event handlers. This is inconsistent with the rest of the CSS architecture and harder to maintain.

```html
<a href="/debug" target="_blank" style="margin-left:auto;padding:6px 14px;font-size:12px;
   color:var(--accent);text-decoration:none;border:1px solid var(--border);border-radius:6px;
   font-family:var(--mono);transition:all 0.15s;"
   onmouseover="this.style.borderColor='var(--accent)'"
   onmouseout="this.style.borderColor='var(--border)'">
  Debug Timeline
</a>
```

**Fix:** Extract to a `.tab-link` CSS class.

---

## 5. Consistency & Code Quality

### 5.1 Inline Styles vs CSS Classes

Several sections use inline `style` attributes for colors and spacing (especially in the Test Suites tab, Compare tab, and Secrets tab) rather than CSS classes. This is inconsistent with the well-structured CSS at the top of the file. While functional, it makes the code harder to maintain and theme.

**Notable examples:**
- Suite card inner divs all use inline `font-size`, `color`, `margin-top`
- Compare controls use inline `display:flex;gap:12px` etc.
- All form labels in Config, Secrets, and Create Suite tabs use inline styles

**Recommendation:** Extract commonly-repeated inline patterns into utility classes (`.label-sm`, `.flex-row`, etc.) during the next refactor cycle.

### 5.2 `--text-label` vs Hardcoded `#475569`

The color `#475569` appears as a literal hex value in numerous places (`.stat-label`, `.log-time`, `.trade-field-label`, etc.) despite `--text-dim` being `#64748b` and `--text-label` being `#94a3b8`. A third "muted" tier exists informally as `#475569` but has no CSS variable name — which is the root cause of the `--muted` bug above.

---

## 6. Prioritized Fix List

| # | Issue | Severity | Estimated Effort |
|---|-------|----------|-----------------|
| 1 | Add `--muted: #475569` to `:root` CSS | 🔴 Critical | 2 min |
| 2 | Update brand/sub-label from Alpaca → Tradovate | 🔴 Critical | 10 min |
| 3 | Replace `alert()`/`confirm()` with toast + styled modal | 🟠 High | 2–3 hrs |
| 4 | Add `overflow-x: auto` to tabs bar for mobile | 🟠 High | 5 min |
| 5 | Add 2-column mobile override for `.trade-grid` | 🟠 High | 5 min |
| 6 | Wrap results table in `overflow-x: auto` div | 🟠 High | 5 min |
| 7 | Remove or replace fake progress bar | 🟠 High | 30 min |
| 8 | Fix stats bar mobile layout (10 items, lone last row) | 🟡 Medium | 15 min |
| 9 | Bump minimum label font size from 9px → 10–11px | 🟡 Medium | 15 min |
| 10 | Add unsaved-changes indicator on Config tab | 🟡 Medium | 1–2 hrs |
| 11 | Add `flex-wrap` to `.trade-header` | 🟡 Medium | 5 min |
| 12 | Extract Debug Timeline link to `.tab-link` class | 🟢 Low | 10 min |
| 13 | Replace inline styles in suites/compare with CSS classes | 🟢 Low | 1 hr |

---

## 7. Summary

The TraderB dashboard is a polished, well-themed application with excellent foundational design decisions. The critical issues are quick fixes: the missing `--muted` variable and the stale Alpaca branding can be resolved in under 15 minutes. The mobile tab overflow and trade grid issues are also straightforward CSS additions.

The medium-term priority should be replacing browser `alert()`/`confirm()` dialogs with a lightweight in-page notification system — this is the biggest gap between the current polished visual design and the actual user interaction quality.

---

*Report generated by Rotem (UX Designer subagent) — 2026-04-12*

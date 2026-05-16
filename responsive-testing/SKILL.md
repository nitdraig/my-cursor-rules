---
name: responsive-testing
description: Open the app in Cursor's browser at multiple viewport sizes, screenshot each, and report any layout breakage. Use after UI changes, before merging frontend work, or when the user asks for responsive testing, breakpoint checks, or mobile layout verification.
user-invocable: true
---

# Responsive Testing

After a UI change, verify the app looks correct at all standard breakpoints.

## Prerequisites

1. **Dev server running** — Check terminal output or `package.json` scripts for the URL (usually `http://localhost:3000`). Start the server if it is not running.
2. **cursor-ide-browser MCP** — Use `browser_navigate`, `browser_resize`, `browser_snapshot`, and `browser_take_screenshot`.
3. **Target page** — Use the URL the user specifies, or the route under test (append path to the dev server base URL).

## Viewports to Test

| Name | Width | Height | Tailwind |
|------|-------|--------|----------|
| Mobile (small) | 375 | 812 | default |
| Mobile (large) | 428 | 926 | default |
| Tablet | 768 | 1024 | `md:` |
| Desktop | 1280 | 800 | `xl:` |
| Ultrawide | 1536 | 900 | `2xl:` |

Use the height column for `browser_resize` so results are consistent across runs.

## Workflow

### 1. Navigate to the Page

```
browser_navigate({ url: "<dev-server-url>" })
```

If a tab already exists, follow the lock workflow: `browser_lock` (lock) before interactions, `browser_lock` (unlock) when fully done.

Wait for the page to load (short incremental waits + `browser_snapshot` until content is ready).

### 2. Test Each Viewport

For each viewport size:

1. Resize with `browser_resize({ width, height })` — do **not** rely on `browser_navigate` for viewport size; it has no width/height parameters.
2. Take a screenshot with `browser_take_screenshot` (use a descriptive `filename`, e.g. `responsive-375-mobile.png`).
3. Run `browser_snapshot` and inspect the aria tree for:
   - Content overflowing or hidden behind other elements
   - Navigation that should collapse into a hamburger menu
   - Text that's too small to read
   - Buttons/links too close together (touch target issues)
   - Horizontal scrollbars that shouldn't exist

Optional: use `browser_take_screenshot({ fullPage: true })` when vertical overflow or below-the-fold layout is in scope.

### 3. Check for Common Breakage

- **Overflow**: Elements wider than the viewport causing horizontal scroll
- **Collapsed layout**: Flex/grid items that should stack on mobile but don't
- **Hidden content**: Elements that disappear at certain sizes without a menu toggle
- **Font scaling**: Text that's readable on desktop but tiny on mobile
- **Fixed positioning**: Modals, toasts, or sticky headers that break on small screens
- **Images**: Oversized images that don't scale down

### 4. Report

Use this template:

```
Responsive Test Results:
  375px (mobile):  PASS — layout stacks correctly
  428px (mobile):  PASS
  768px (tablet):  WARN — nav items overlap, need hamburger menu
  1280px (desktop): PASS
  1536px (ultrawide): WARN — content not centered, stretched too wide
```

Status values: **PASS**, **WARN**, **FAIL**. Include a one-line reason for WARN/FAIL.

Attach or reference screenshots for any WARN/FAIL viewport.

### 5. Fix and Re-test

If issues are found and the user wants fixes:

1. Fix layout/CSS in the codebase (prefer Tailwind responsive utilities matching the breakpoint column).
2. Re-run steps 2–4 **only for affected viewports**.
3. Update the report until all viewports are PASS or remaining issues are documented as accepted trade-offs.

## Checklist

Copy and track progress:

```
- [ ] Dev server URL confirmed
- [ ] Page loaded at target route
- [ ] 375px — screenshot + snapshot
- [ ] 428px — screenshot + snapshot
- [ ] 768px — screenshot + snapshot
- [ ] 1280px — screenshot + snapshot
- [ ] 1536px — screenshot + snapshot
- [ ] Report written
- [ ] Fixes applied (if requested) and affected viewports re-tested
```

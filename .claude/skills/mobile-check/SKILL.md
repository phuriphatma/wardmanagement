---
name: mobile-check
description: Verify mobile/phone layout of the Ward Manager app when the automation viewport is locked at 1440px and real phone rendering can't be screenshotted. Use when changing responsive CSS, the slide-over sidebar, the bottom tab bar, or any ≤768px behaviour, or when the user reports something "doesn't show on mobile".
---

# Mobile layout check

The browser-automation viewport here is locked at ~1440px, so you **cannot screenshot true
phone rendering**. Verify phone behaviour with these techniques instead, then ask the user
to confirm on-device.

## 1. Reason about the cascade first

For "element is invisible on phone" bugs, check **both**:

1. **Which rule wins.** Phone rules in earlier `app.css` blocks can be overridden by the
   later `modern-ui` block (same specificity + `!important` → source order wins). Scope
   desktop rules with `@media (min-width:769px)` so they don't leak down.
2. **Transformed ancestors.** The slide-over `#sidebar` uses `transform`. Any
   `position:fixed` descendant is then positioned relative to the *sidebar*, not the
   viewport — it can land off-screen. Reparent such elements to `<body>` on open.

Breakpoints: slide-over + tab bar + mobile chips activate at **≤768px**.

## 2. Read computed styles at a simulated width

If the browser extension is connected, open `index.html`, then resize to a phone width and
inspect the element — don't trust the 1440px view:

```js
// via mcp__claude-in-chrome__resize_window to e.g. 390x844, then javascript_tool:
const p = document.getElementById('profileMenuPanel');
p && getComputedStyle(p).position + ' top=' + getComputedStyle(p).top +
    ' display=' + getComputedStyle(p).display;
```

A `top` near `100vh`, or a `position`/containing-block surprise, is the smoking gun.

## 3. Static syntax + cascade check (no browser)

```
node --check assets/app.js
grep -nE '@media \(max-width|@media \(min-width' assets/app.css   # see the breakpoints in play
```

## 4. Always hand off to the user

End mobile changes by asking the user to confirm on their actual phone, and remind them a
stale GitHub Pages deploy can masquerade as a layout bug (hard-refresh first).

Record any new gotcha in `docs/MISTAKES.md`.

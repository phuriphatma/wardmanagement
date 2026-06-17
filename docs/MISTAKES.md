# Mistakes & gotchas

Hard-won lessons. Read before touching CSS, mobile layout, or the profile menu.
Add a dated entry whenever a bug's root cause was non-obvious.

## 2026-06-17 — Profile menu invisible on phones (two stacked CSS bugs)

**Symptom:** On desktop, tapping *Profile* opened the select-popup; on phones, nothing
appeared.

**Cause 1 — cascade clobber.** A `@media (max-width:640px)` rule set
`#profileMenuPanel` to `position:fixed; top:12px`, but a *later*, unscoped `modern-ui`
rule (`#profileMenuPanel{ … top:calc(100% + 6px) !important }`) won the same-specificity
`!important` tie by source order. For a fixed element, `top:calc(100% + 6px)` ≈ `100vh` →
the panel sat just below the viewport. **Lesson:** the `modern-ui` block loads last and
will override earlier blocks; scope desktop-only rules with `@media (min-width:769px)` so
they don't leak onto phones.

**Cause 2 — transformed ancestor = containing block.** The panel lives inside `#sidebar`,
which uses a CSS `transform` for its slide-over. A transformed ancestor becomes the
containing block for `position:fixed` descendants, so even a correct `top:12px` was
measured from the (translated, off-screen) sidebar box. **Fix:** on phones, JS reparents
`#profileMenuPanel` to `<body>` while open (and restores it for the desktop popover).
See `openProfilePanel`/`closeProfilePanel` in `app.js`.

## CSS cascade order is load-bearing

`app.css` = three blocks concatenated in source order; `modern-ui` is intentionally last.
Same-specificity `!important` rules are resolved by source order, so reordering the blocks
silently changes which rule wins. Don't reorder; add new rules in the right block.

## The dark Quick-Note editor is theme-scoped

`#profilePage` keeps its own dark theme via higher-specificity `#profilePage …` selectors.
Generic token changes won't reach it; target `#profilePage` explicitly.

## Mobile can't be screenshotted here

The browser-automation viewport is locked at 1440px, so real phone rendering can't be
captured. Verify mobile by computing the cascade, using the `mobile-check` skill (inject
phone media rules / read computed styles), and asking the user to confirm on-device.

## "I don't see it on my phone" ≠ bug

`main` is served by GitHub Pages, which can lag or cache. Before chasing a rendering bug,
rule out a stale deploy (hard refresh / check the deployed commit). This previously caused
a false alarm about the bottom tab bar not appearing.

## Don't tear down focused fields on mobile

Full re-renders that rebuild a focused input cause the on-screen keyboard to close/reopen
("dizzy"). Prefer incremental DOM updates + synchronous focus (`buildWWBlock`/`makeRow`).

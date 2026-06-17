# CLAUDE.md — Ward & Bed Manager

Project guide for Claude Code. Read this first; follow the linked docs for detail.

## What this is

A **single-page web app** for Thai hospital ward/bed management, used by a clinician
**at the bedside** (Android phone / iPad / desktop). No backend, no build step — it is
plain HTML/CSS/JS served as static files (GitHub Pages). All data lives in the browser's
`localStorage`; backups are encrypted files (CryptoJS) the user exports/imports.

Core features: Wards → Beds (patients) · Quick Ward Note with morning/afternoon I/O
(เช้า/บ่าย) calculator · Ward Work task lists · Notes · Google Sheet import · encrypted
export/import · multiple **profiles** (workspaces).

## File layout

```
index.html        HTML structure only (was a single 5,790-line file; split 2026-06-17)
assets/app.css    All styles (3 cascade layers concatenated in order; modern-ui is last)
assets/app.js      All behaviour, wrapped in one IIFE (no globals leak; no inline handlers)
docs/ARCHITECTURE.md   Section map of app.js, data model, storage keys
docs/STATE.md          Current state, branch status, open tasks (update as you go)
docs/MISTAKES.md       Hard-won gotchas — READ before touching CSS/mobile
.claude/skills/        Project skills (e.g. mobile-check)
```

External deps load from CDN in `<head>` **before** `app.js`: CryptoJS (encryption),
SheetJS/XLSX (sheet import), Inter font. Keep that order.

## How to run

It's static — just open `index.html` in a browser (`file://` works) or use the `run`
skill. There is no `npm install`, no server, no tests. After editing `app.js`, sanity-check
syntax with `node --check assets/app.js`.

## Conventions (match the existing code)

- **One IIFE, no globals.** `app.js` is `(function(){ … })();`. Everything is module-private.
  There are **no inline `on*` HTML handlers** — all wiring is `el.addEventListener`/`el.onclick`
  in JS. Keep it that way (an inline handler can't see IIFE-scoped functions).
- **CSS cascade order matters.** `app.css` is three blocks in source order; the
  `modern-ui` block is **last on purpose** so it refines the earlier ones. A later
  same-specificity `!important` rule wins — don't reorder blocks. See MISTAKES.md.
- **Incremental DOM updates over full re-renders** where a field is focused (mobile
  keyboard jank). See `buildWWBlock`/`makeRow` in Ward Work.
- **Plain, well-ordered wording** in UI — users are non-technical clinicians.
- Visual tokens live in `:root` of the modern-ui block (`--bg`, `--primary:#2563eb`,
  green=active / red=discharge, radius 10–14px). Reuse `.btn/.btn-primary/.btn-ghost/.btn-danger`.

## Working rules

- **Bedside speed & mobile smoothness** are the top UX priorities. Test mobile changes —
  but note the browser-automation viewport here is locked at 1440px, so **true phone
  rendering can't be screenshotted**; verify with the `mobile-check` skill and lean on the
  user to confirm on-device.
- This is a large single JS file. Prefer **localized, well-commented** changes over
  wholesale rewrites.
- **`main` is the live site** (GitHub Pages). Only push to `main` when the user asks.
- GitHub Pages can lag/cache; "I don't see it on my phone" may be a deploy delay, not a bug.

When you learn something non-obvious, add it to `docs/MISTAKES.md` and update `docs/STATE.md`.

# State & roadmap

Living status doc. Update when you finish work or learn something that changes the plan.

## Current state (2026-06-17)

- **Code structure:** split the former single 5,790-line `index.html` into
  `index.html` + `assets/app.css` + `assets/app.js`. Behaviour is unchanged (verbatim
  move); CDN deps still load in `<head>` before `app.js`.
- **Profile menu on phones: FIXED.** Was invisible (off-screen) on phones; now opens as a
  fixed top sheet with a backdrop, reparented to `<body>` so the sidebar's transform can't
  drag it off-screen. See MISTAKES.md (2026-06-17). The desktop popover is unchanged.
- **Mobile navigation:** slide-over sidebar + fixed bottom tab bar
  (Beds · Work · ⚡Quick · Notes · ☰ More), ward picker dropdown, and mobile profile chip.
- **Project memory system:** added `CLAUDE.md`, `docs/`, and `.claude/skills/`.

## Branches & deploy

- `main` = the live GitHub Pages site. Push there only when the user asks.
- Mobile nav work was developed on `development`. As of this change it is being
  promoted to `main` at the user's request.

## Verify after deploy

- [ ] On a real phone: tap the **Profile** chip on the Beds view → the profile sheet
      appears at the top with the profile list + actions; tapping the backdrop closes it.
- [ ] Bottom tab bar shows and switches views (rule out GitHub Pages cache first).
- [ ] Desktop *Profile* button still opens the popover under it (regression check).

## Open ideas / not done

- The Ward Work / Notes "Document" popovers (`#wwProfileMenuPanel`, `#notesProfileMenuPanel`)
  use the older `position:absolute; right:0` pattern. They weren't reported broken, but if a
  phone complaint surfaces, apply the same fixed-sheet treatment as the profile panel.
- `app.js` is still one large file. If it grows further, consider splitting by section
  (profiles / wards-beds / ward-work / notes) — but only as ordered classic `<script>`
  files (preserve load order; the IIFE/global assumptions still hold).

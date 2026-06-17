# Ward & Bed Manager

A single-page web app for hospital ward/bed management at the bedside: wards → beds
(patients), Quick Ward Note with morning/afternoon I/O (เช้า/บ่าย) calculator, Ward Work
task lists, Notes, Google Sheet import, and encrypted backup/restore. No backend — all data
is stored in the browser (`localStorage`); backups are encrypted files.

## Run

It's static. Open `index.html` in any modern browser (or serve the folder). No build step,
no dependencies to install. Deployed via GitHub Pages.

## Structure

| Path | Purpose |
|---|---|
| `index.html` | HTML structure |
| `assets/app.css` | Styles |
| `assets/app.js` | All behaviour (one IIFE) |
| `docs/` | Architecture, current state, and gotchas |
| `CLAUDE.md` | Guide for working in this repo (with Claude Code) |

## Contributing / working in this repo

See [`CLAUDE.md`](CLAUDE.md) and [`docs/`](docs/). After editing `app.js`, run
`node --check assets/app.js`. For mobile changes, see the `mobile-check` skill — and test on
a real phone.

# Architecture

`assets/app.js` is one IIFE (~4,500 lines). It has no module system; sections are just
ordered top-to-bottom and share the IIFE scope. This is the map.

## Data model

A **profile** is a workspace. The active profile's `data` is the working object:

```
profileStore = { current: <profileId>, map: { <id>: { id, name, data, sheetConfig? } } }
data         = { wards: [ { id, name, beds: [ { id, label, name?, dx?, hn?, status, note?… } ] } ] }
```

`status` is `'active'` (green) or `'discharge'` (red). Ward Work and Notes have their own
parallel "profiles" (documents). I/O draft notes are saved separately and attached to a
ward later.

## localStorage keys

| Key | Holds |
|---|---|
| `wardManagerProfiles-v1` | the whole `profileStore` (primary store) |
| `wardManagerData` | legacy single-profile data (migrated on load) |
| `wardManagerDraftNote-v1` | the unattached Quick I/O draft note |
| `wardWorkProfiles-v2` / `wardWorkCurrent-v2` | Ward Work documents + current id |
| `io-snippets-v1`, `ww-snippets-v1` | reusable I/O / Ward Work snippets |
| `wardPresets-v1` | named ward-list presets |
| `wardStatusFilter-v1`, `wardListOnlyBeds-v1`, `wardsEditMode` | view prefs |
| `wardSidebarWidth`, `ww-preview-open-v1`, `ww-mobile-*` | layout prefs |

## Section map of app.js (search these function names)

- **Persistence & profiles** — `saveProfiles`/`loadProfiles`/`switchProfile`/`saveData`,
  `createProfile`/`renameProfile`/`deleteProfile`, `refreshProfileUI`.
- **Profile panel UI** — `openProfilePanel`/`closeProfilePanel`/`toggleProfilePanel`,
  `ensureProfileBackdrop`. (Phone behaviour: reparented to `<body>`; see MISTAKES.md.)
- **Wards & beds rendering** — `renderWards`, `renderBeds`, `renderWardPicker`, `goToWard`.
- **Bed selection / drag-move** — `toggleBedSelection`, `getWardAtPoint`,
  `startMobileDrag`/`updateMobileDrag`/`endMobileDrag`, `moveBedsBetweenWards`.
- **Ward presets** — `loadPresets`/`savePresets`/`refreshPresetSelect`/`openPresets`.
- **Google Sheet import** — `openSheetProfileModal`, `parseCSV`, `normalizeGoogleCsvUrl`,
  `extractSheetId`, `renderSheetPreview`, `buildDataFromSheetRows`, column-pick (`startPick`).
- **Sidebar resize** — `startResize`/`doResize`/`stopResize`.
- **Move-bed modal** — `openMoveBedModal`/`populateMoveWards`.
- **Ward Work** — `loadWWProfiles`/`saveWWProfiles`/`switchWWProfile`, `openWardWork`,
  `renderWW`, `buildWWBlock`, `addEmptyTask`, block/task drag (`enableMobileBlockDrag`,
  `moveTaskAcrossBlocks`), preview (`updateWWPreview`, `formatWWHeader`), snippets.
- **Notes** — `loadNotesProfiles`/`saveNotesProfiles`, `openNotes`, `renderNotesContent`,
  `makeCheckboxesClickable`, `normalizeContentStructure`.
- **Quick I/O draft** — `loadDraft`/`saveDraft`/`openDraftNote`/`confirmSaveDraft`,
  `setProfileChrome`.
- **Patient note editor (`#profilePage`)** — `openProfilePage`/`backToBeds`. Dark theme,
  scoped by `#profilePage` specificity.
- **Encrypted backup** — export/import via CryptoJS (search `CryptoJS`).

## Mobile navigation (added on the `development` branch)

- ≤768px the sidebar becomes a **slide-over** (CSS `transform`); `body.collapsed` hides it.
- A fixed **bottom tab bar** (`#mobileTabBar`): Beds · Work · ⚡Quick · Notes · ☰ More.
  A `MutationObserver` on the page containers drives `body[data-view]` + the active tab.
- `#wardPicker` (dropdown) and `#mobileProfileChip` give phone access to ward switching and
  the profile menu without opening the sidebar.

# CLAUDE.md

## Project Overview

Repertoire is a single-page web app for managing a musical repertoire. It runs on an iPad via GitHub Pages with no backend or build step required.

## Architecture

- **Single-file app**: Everything (HTML, CSS, JavaScript) is in `index.html`
- **No build step**: Plain vanilla JS, no frameworks or transpilation
- **Storage**: Uses `localStorage` for persistence; seeds from `repertoire.json` on first load
- **Hosting**: Static files served via GitHub Pages

## Files

| File | Description |
|------|-------------|
| `index.html` | Entire app — HTML, CSS, JavaScript in one file |
| `repertoire.json` | Seed data loaded on first visit |

## Data Format

```json
{
  "songs": [
    {
      "id": "unique-id",
      "title": "Song Title",
      "tags": "Neil Young, rock, acoustic",
      "key": "G",
      "capo": "2",
      "harp": "G",
      "ugLink": "https://tabs.ultimate-guitar.com/...",
      "crd": "Am        C          G\nLyric line one..."
    }
  ],
  "setlists": [
    {
      "id": "unique-id",
      "name": "Setlist Name",
      "items": [
        { "type": "section", "name": "Set 1", "collapsed": false },
        { "type": "song", "songId": "id1" },
        { "type": "section-end" },
        { "type": "song", "songId": "id2", "altSongId": "id5", "useAlt": false },
        { "type": "section", "name": "Encore", "collapsed": true },
        { "type": "song", "songId": "id3" }
      ]
    }
  ],
  "tuneUp": false
}
```

- `tuneUp` (top-level, global): tracks whether the "Half-step down tuning" header toggle is currently on. Flipping it bumps (or restores) every song's `capo` field by 1 fret across the whole library (a confirmed bulk edit, `toggleGlobalTune`); the CRD reader's own Capo readout folds that bump back in on top of the root/playing-key distance (`computeCapo`).

- `song` items resolve to `altSongId` instead of `songId` when `useAlt` is true — lets a slot's live song be swapped without reordering.
- `section` items are collapsible chapter headings that live inline in the same flat `items` array — every `song` item after one belongs to it by position (not by any nested/reference structure) up to the next boundary: another `section`, an explicit `section-end`, or the end of the list. `collapsed` just controls whether those rows are rendered; it never changes what plays, prints, or counts.
- `section-end` items are an invisible boundary marker with no other fields — they close a section early instead of letting it run to the next header. See "Sections" below.
- Legacy setlists with a flat `songIds: [id, ...]` array are migrated to `items` on load (`migrateSetlists()`), which also silently drops any leftover `subset` items from the retired embed-another-setlist feature (replaced by `section`).

## Key Implementation Details

- **Theme**: Deep blue-black / muted teal (`#3d9e8c`) studio/DAW aesthetic
- **Visualizer**: Animated glass bubbles packed with a circle-packing algorithm, grouped by tag; bubble size proportional to song count
- **Songs table**: Inline editing (click row or ✎ button); sortable columns; tag autocomplete dropdown on tags fields
- **Quick UG Import**: Parses title and artist from a UG URL slug — no network request needed. Pattern: `/tab/{artist}/{title}-{type}-{id}`
- **Ultimate Guitar links**: Normalized to `tabs.ultimate-guitar.com` Universal Link domain for iOS app compatibility
- **CRD reader**: A song can carry a `crd` field — a plain-text chord/lyric sheet, entered manually (Add Song modal, inline edit, or the full-page editor). When a song has `crd`, its title everywhere (songs table, setlists, play mode, visualizer pills) opens the in-app CRD reader (`openCrdReader`) instead of the UG link. Because it's an in-page overlay rather than a navigation, it also sidesteps the AirTurn/UG Universal-Link app-switch issue during Play mode.
  - **Full-screen overlay** (`#crd-reader`), not a small modal. The header/toolbar (`.crd-chrome`) is tap-to-toggle — tapping the sheet hides it for more reading room, tapping again brings back the "← Back" button and controls.
  - **Chord detection**: recognizes chords either inline as `[G]` (ChordPro-style) or on their own "chord line" above the lyrics, including slash/bass chords (`G/D`, tolerant of spaces `G / D` and parens), augmented (`aug`/`+`), and bar/repeat notation (`|`, `%`, `N.C.`) without breaking detection of the real chords on that line. `[Verse]`/`[Chorus]`/etc. in brackets render as section labels, not chords.
  - **Root key vs. Playing key**: `s.crd` is always stored written in the song's `key` (root). The reader header shows two editable note-name inputs — **Key** (`crd-key-input` → `crdEditKey`, the root) and **Playing key** (`crd-tkey-input` → `crdEditTKey`, the shape actually being played) — plus a read-only **Capo** badge computed from the two: `capo = (semitone(root) − semitone(playingKey) + 12) % 12 + (tuneUp ? 1 : 0)` (`computeCapo`). Opening the reader defaults Playing key to whatever's implied by the song's saved `key`/`capo`/tuning (`deriveDefaultTKey`) — the equivalent of the old auto-capo baseline, just expressed as a note name.
  - Key/Playing-key inputs accept a root note plus an optional quality restricted to `KEY_QUALS` (`''` major, `m` minor, `mmaj7` minor-major-7) — nothing else (`parseKeyName`/`formatKeyName`; e.g. `Am`, `F#`, `Gmmaj7` parse, `Am7`/`Csus4` don't). Only the pitch matters for the capo/transpose math, but `deriveDefaultTKey` and the −/+ steppers carry the quality through unchanged so e.g. an `Am` song defaults to (and steps between) minor playing keys, not major.
  - Editing **Playing key** (−/+ semitone steppers `crdTKeyStep`, ↺ `crdTKeyReset` back to the saved default, or typing directly) live re-transposes the on-screen chords (`crdChordsEff`, computed against the song's *actual saved* key, not any pending root edit) and updates the Capo badge. Editing **Key** (the root) only updates the Capo badge/preview — it never touches the displayed chords or the saved sheet until committed.
  - **Committing** (`crdCommit`, bound to `#crd-store-btn`): label/behavior depend on what's dirty. If only the resulting capo differs from what's saved, it reads **"Save capo"** and just writes the new `s.capo`. If the root key was edited, it reads **"Update root"** and additionally shifts `s.crd` by the exact semitone interval between the old and new root (`transposeCrdText`) and writes the new `s.key` — i.e. reloading without committing always reverts to the last-saved root. `crdUpdateStoreBtn()` keeps the label/enabled state in sync on every keystroke via the lightweight `crdMetaEdited` (which deliberately avoids rebuilding `#crd-meta`, to not steal focus mid-typing).
  - **Half-step tuning and the reader**: `tuneUp` bumping every song's stored `capo` (via `toggleGlobalTune`) never changes which chord shapes are shown — those come purely from root-vs-playing-key — only the Capo badge, which folds the tuning bump back in via `computeCapo`.
  - No delete button in the reader — deleting a song only happens from the Songs tab, so it's never one misplaced tap away while reading/playing.
  - **Full-page editor** (`#crd-editor`, `openCrdEditor`/`closeCrdEditor`): the "⤢ Full editor" button next to the CRD field (Add Song modal `f-crd`, Songs table inline edit `ie-crd`, or setlist inline edit `sl-ie-crd`) pops a full-screen split view — the raw textarea plus a live-rendered preview (`renderCrdEditorPreview`, same `renderCrdHTML` the reader uses, always at transpose 0 — no playing-key/capo math here) side by side, updating on every keystroke. It also carries just the root **Key** field (`crdEditorKey`), no Playing key/Capo — it works even for a not-yet-saved song (no `db.songs` entry to read) by deriving the sibling Key field id straight from `targetId` (`"f-crd"` → `"f-key"`, `"ie-crd"` → `"ie-key"`, etc.). On "← Done", the crd text *and* Key sync back into those sibling fields (`dispatchEvent('input')`, so the outer form/Save picks them up). Doubles as the fix for a sheet transcribed in the wrong key — edit the raw chords directly and update Key alongside them; no separate "re-root" tool needed (there used to be one in the reader itself, removed once this covered the same case).
  - **Keyboard**: Up/Down/PageUp/PageDown scroll the sheet, Escape closes the reader (ignored while typing in a text field).
  - No chord/fretboard diagrams — removed after trying a diagrams-strip and a tap-to-popup version; the reader is text-only now.
- **Setlists**: Drag-and-drop reordering via HTML5 drag API; touch-friendly ↑↓ buttons
  - **Inline song editing from a setlist row**: the ✎ button on a `slRow` opens the exact same fields as the Songs tab's inline edit (title/Key/Capo/Harp/Artists/attrs/UG link/CRD) — it edits the underlying song, not anything setlist-specific. Tracked via a separate `slEditSongId` (not `inlineEditId`), with its own `sl-ie-*` input ids and `slEditRow`/`startSlEdit`/`saveSlEdit`/`cancelSlEdit`, so it can never collide on DOM ids with the Songs tab's `songEditRow`/`inlineEditId` if the same song is (at least technically) in an editing state in both places at once. `attrSelectHTML(s, grp)` builds the one `<select>` shared by both forms so they can't drift apart.
- **Alternate songs**: A song slot can carry an `altSongId`; the ⇄ button flips which one (`useAlt`) is live, for last-minute swaps without reordering
- **Sections**: A setlist can carry `type:'section'` header items to group its songs into collapsible chapters (Set 1, Encore, …) — purely a display grouping over the same flat `items` array, not a separate structure. A section runs open-ended — everything after it belongs to it — until the next boundary: another `section` header, an explicit `type:'section-end'` marker, or the end of the list. `section-end` is purely structural: it renders nothing (`renderSetDetail`'s grouping loop just resets `nested`/`collapsed` on hitting one) and only exists to let a section close *before* the next header, instead of running on to it. Collapsing a section (`collapsed`) just hides the song rows under it; it never affects `resolvePlayableSongs()`, which is a plain `map`+`filter` over all items (section/section-end items resolve to no song via `resolveItemSong` and are skipped automatically). New songs are inserted right after whichever row is currently selected (`selectedItemIdx`), so selecting a row inside (or the header of) a section adds to that section. New sections (`saveSection`) are inserted right after the selection too, but a real song immediately following the insertion point (i.e. not already another boundary) gets an auto-inserted `section-end` spliced in right after the new header — so it starts empty and that song stays exactly where it was — matching how a real chapter break works: open-ended is fine for a trailing section like "Encore" with nothing after it, but a section inserted mid-list needs an explicit end so it doesn't swallow what was already there. No UI removes a `section-end` directly (there's no visible row for one) — deleting the section header that precedes one just leaves it in place as an inert boundary, which is harmless since it isn't reachable/owned by anything else.
- **Play mode**: Scroll-snap drum-roll UX — fixed 100px items, CSS `scroll-snap-type: y mandatory`, centered item detected via `scrollend` + debounced `scroll`. Auto-advances on return from UG via `visibilitychange`
- **GitHub push**: Uses GitHub Contents API (PUT) to commit `repertoire.json` directly; config stored in `localStorage`
- **Repo sync**: On boot, fetches `repertoire.json` and compares fingerprints; shows banner if remote differs
- **Viewport fix**: Resets viewport meta on `visibilitychange` to prevent iPad Safari zoom-out on app resume

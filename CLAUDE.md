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
        { "type": "song", "songId": "id1" },
        { "type": "song", "songId": "id2", "altSongId": "id5", "useAlt": false },
        { "type": "subset", "setlistId": "other-setlist-id", "optional": true, "included": false }
      ]
    }
  ],
  "tuneUp": false
}
```

- `tuneUp` (top-level, global): tracks whether the "Half-step down tuning" header toggle is currently on. Flipping it does two things at once — bumps (or restores) every song's `capo` field by 1 fret across the whole library (a confirmed bulk edit), and adds ±1 semitone on top of whatever the CRD reader is currently showing (`crdEffectiveTranspose = crdTranspose + (tuneUp ? 1 : 0)`).

- `song` items resolve to `altSongId` instead of `songId` when `useAlt` is true — lets a slot's live song be swapped without reordering.
- `subset` items pull another setlist's songs in live (not duplicated); they only count toward playback/print/totals when `included` is true.
- Legacy setlists with a flat `songIds: [id, ...]` array are migrated to `items` on load (`migrateSetlists()`).

## Key Implementation Details

- **Theme**: Deep blue-black / muted teal (`#3d9e8c`) studio/DAW aesthetic
- **Visualizer**: Animated glass bubbles packed with a circle-packing algorithm, grouped by tag; bubble size proportional to song count
- **Songs table**: Inline editing (click row or ✎ button); sortable columns; tag autocomplete dropdown on tags fields
- **Quick UG Import**: Parses title and artist from a UG URL slug — no network request needed. Pattern: `/tab/{artist}/{title}-{type}-{id}`
- **Ultimate Guitar links**: Normalized to `tabs.ultimate-guitar.com` Universal Link domain for iOS app compatibility
- **CRD reader**: A song can carry a `crd` field — a plain-text chord/lyric sheet, entered manually (Add Song modal, inline edit, or the full-page editor). When a song has `crd`, its title everywhere (songs table, setlists, play mode, visualizer pills) opens the in-app CRD reader (`openCrdReader`) instead of the UG link. Because it's an in-page overlay rather than a navigation, it also sidesteps the AirTurn/UG Universal-Link app-switch issue during Play mode.
  - **Full-screen overlay** (`#crd-reader`), not a small modal. The header/toolbar (`.crd-chrome`) is tap-to-toggle — tapping the sheet hides it for more reading room, tapping again brings back the "← Back" button and controls.
  - **Chord detection**: recognizes chords either inline as `[G]` (ChordPro-style) or on their own "chord line" above the lyrics, including slash/bass chords (`G/D`, tolerant of spaces `G / D` and parens), augmented (`aug`/`+`), and bar/repeat notation (`|`, `%`, `N.C.`) without breaking detection of the real chords on that line. `[Verse]`/`[Chorus]`/etc. in brackets render as section labels, not chords.
  - **Transpose** (`crdTranspose`, −11..+11): −/+/reset controls, plus **Store transposed** (`crdStoreTransposed`) which rewrites the saved `crd` text at the current transpose and resets the control — Key and Capo fields are left untouched.
  - **Capo auto-transpose**: opening the reader defaults `crdTranspose` to `-capo` (`crdBaseTranspose`), showing the shapes to play with the capo on; Reset returns to this baseline, not to 0.
  - **Full-page editor** (`#crd-editor`, `openCrdEditor`/`closeCrdEditor`): the "⤢ Full editor" button next to the CRD field (Add Song modal or inline edit) pops a full-screen textarea for comfortable long-sheet editing, writing back into that field on "← Done".
  - **Keyboard**: Up/Down/PageUp/PageDown scroll the sheet, Escape closes the reader (ignored while typing in a text field).
  - No chord/fretboard diagrams — removed after trying a diagrams-strip and a tap-to-popup version; the reader is text-only now.
- **Setlists**: Drag-and-drop reordering via HTML5 drag API; touch-friendly ↑↓ buttons
- **Alternate songs**: A song slot can carry an `altSongId`; the ⇄ button flips which one (`useAlt`) is live, for last-minute swaps without reordering
- **Sub-setlists**: A setlist can embed another setlist as an optional, collapsible block (`type:'subset'`); toggling `included` pulls its songs into playback/print/counts live, no duplication. `resolvePlayableSongs()` flattens items recursively with a cycle guard
- **Play mode**: Scroll-snap drum-roll UX — fixed 100px items, CSS `scroll-snap-type: y mandatory`, centered item detected via `scrollend` + debounced `scroll`. Auto-advances on return from UG via `visibilitychange`
- **GitHub push**: Uses GitHub Contents API (PUT) to commit `repertoire.json` directly; config stored in `localStorage`
- **Repo sync**: On boot, fetches `repertoire.json` and compares fingerprints; shows banner if remote differs
- **Viewport fix**: Resets viewport meta on `visibilitychange` to prevent iPad Safari zoom-out on app resume

# Repertoire

A single-page web app for managing a musical repertoire — built to run on an iPad via GitHub Pages, with no backend or build step required.

## Features

### Visualizer
- Songs displayed as animated floating glass bubbles, grouped by tag
- Bubble size scales with the number of songs in the group
- Click a bubble to open an overlay listing all songs in that group with Key / Capo / Harp badges
- Click a song title to open it in Ultimate Guitar

### Songs
- Sortable, searchable table of all songs
- Add, inline-edit, and delete songs
- Each song stores: Title, Tags, Key, Capo, Harp, Ultimate Guitar link
- Key / Capo / Harp shown as badge chips in all views
- **Quick UG Import** — paste a Ultimate Guitar URL to auto-detect title and artist
- **CRD reader** — paste in your own chord/lyric sheet per song (inline `[G]` chords or a chord line above the lyrics, `[Verse]`/`[Chorus]` section labels); if present, the title opens it in a full-screen in-app reader instead of Ultimate Guitar, with a UG link, edit, and delete still one tap away
  - **Transpose** on the fly, with a **Store transposed** button to bake the shift into the saved sheet
  - Auto-transposes for **Capo** (shows the shapes to play with the capo on) and for the global **Half-step down tuning** toggle
  - Tap-to-hide header for a distraction-free view; a full-page editor for comfortable long-sheet editing; keyboard scrolling (arrow keys / Page Up/Down)
- Tag autocomplete when editing — suggests existing tags as you type

### Setlists
- Create and manage multiple setlists
- Two-column layout: setlist selector on the left, song list on the right
- Add songs, remove songs, reorder by drag-and-drop or ↑↓ buttons
- **Alternate songs** — give any song slot a backup; swap which one is live with one tap
- **Sections** — collapsible chapter headings within a setlist (e.g. "Set 1", "Encore"); add songs directly under a section, collapse it to a single row, expand/collapse all with one tap
- **Play mode** — scroll-snap drum-roll view (like iOS date picker): current song centered and large, others dimmed; auto-advances when returning from Ultimate Guitar

### Data
- **Half-step down tuning** — a global header toggle for instruments tuned down a half step; bumps every song's Capo by 1 fret (library-wide, confirmed) and shifts the CRD reader's transpose to match
- All data stored in `localStorage` (persists across sessions on the same device)
- On first load, seeds from `repertoire.json` if no saved data exists
- Detects when the repo file differs from local data and offers to sync
- **Export** — downloads current state as `repertoire.json`
- **Import** — loads a previously exported JSON file
- **Push to GitHub** — pushes `repertoire.json` directly to the repo via GitHub API

## Hosting on GitHub Pages

1. Push `index.html` and `repertoire.json` to the `main` branch
2. In the repo settings → **Pages** → set source to `main` / `root`
3. Access at `https://<username>.github.io/<repo>/`

The app works fully offline after the first load (localStorage persistence).

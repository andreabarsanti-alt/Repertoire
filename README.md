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
- **CRD reader** — paste in your own chord/lyric sheet per song (inline `[G]` chords or a chord line above the lyrics, `[Verse]`/`[Chorus]` section labels); if present, the title opens it in a full-screen in-app reader instead of Ultimate Guitar, with a UG link and edit still one tap away (no delete here — that's Songs-tab-only, so it's never a stray tap away mid-reading)
  - **Root key vs. Playing key** — type the key you're actually playing shapes in and the header live-shows the **Capo** you need (and re-transposes the chords on screen); edit the **Key** itself to preview a different root without touching the sheet. Nothing's saved until you tap **Save capo** (just remembers the capo) or **Update root** (also rewrites the sheet into the new key) — reload without tapping either and it's exactly as it was
  - Auto-defaults to the song's saved Capo/Key on open, and folds in the global **Half-step down tuning** toggle
  - Tap-to-hide header for a distraction-free view; keyboard scrolling (arrow keys / Page Up/Down)
  - **⤢ Full editor** — a full-screen split view for comfortable long-sheet editing: the raw text on one side, a live rendering (styled chords, section labels — the same look as the reader) on the other, updating as you type. Just a Key field alongside it (no Playing key/Capo here) — it saves straight into the song when you tap Done, and it works even before the song's been saved. Also the go-to for fixing a sheet transcribed in the wrong key — edit the chords and Key together, right there
- Tag autocomplete when editing — suggests existing tags as you type

### Setlists
- Create and manage multiple setlists
- Two-column layout: setlist selector on the left, song list on the right
- Add songs, remove songs, reorder by drag-and-drop or ↑↓ buttons
- **✎ Edit right from the row** — same full edit form as the Songs tab (Title, Key, Capo, Harp, Artists, UG link, CRD, custom attributes), just without leaving the setlist
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

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
- Tag autocomplete when editing — suggests existing tags as you type

### Setlists
- Create and manage multiple setlists
- Two-column layout: setlist selector on the left, song list on the right
- Add songs, remove songs, reorder by drag-and-drop or ↑↓ buttons
- **Alternate songs** — give any song slot a backup; swap which one is live with one tap
- **Sub-setlists** — embed another setlist as an optional block (e.g. an encore) and toggle it in or out per show
- **Play mode** — scroll-snap drum-roll view (like iOS date picker): current song centered and large, others dimmed; auto-advances when returning from Ultimate Guitar

### Data
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

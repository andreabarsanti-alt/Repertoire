# Repertoire

A single-page web app for managing a musical repertoire — built to run on an iPad via GitHub Pages, with no backend or build step required.

## Features

### Visualizer
- Songs displayed as animated floating bubbles, grouped by **Artist**, **Tag**, or **Setlist**
- Bubble size scales with the number of songs in the group
- Click a bubble to open an overlay listing all songs in that group
- Click a song title to open it directly in Ultimate Guitar

### Songs
- Full sortable, searchable table of all songs
- Add, edit, and delete songs
- Each song stores: Title, Artist, Tags, Key, Capo, Harp, Ultimate Guitar link
- Key / Capo / Harp shown as white badge chips next to the title in all views

### Setlists
- Create and manage multiple setlists / gig setlists
- Two-column layout: setlist selector on the left, song list on the right
- Add songs, remove songs, reorder by drag-and-drop or ↑↓ buttons
- Click a song title to open it in Ultimate Guitar

### Data
- All data stored in `localStorage` (persists across sessions on the same device)
- On first load, seeds from `repertoire.json` if no saved data exists
- **Export** — downloads current state as `repertoire.json`
- **Import** — loads a previously exported JSON file

## Hosting on GitHub Pages

1. Push `index.html` and `repertoire.json` to the `main` branch
2. In the repo settings → **Pages** → set source to `main` / `root`
3. Access at `https://<username>.github.io/<repo>/`

The app works fully offline after the first load (localStorage persistence).

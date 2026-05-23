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
      "ugLink": "https://tabs.ultimate-guitar.com/..."
    }
  ],
  "setlists": [
    {
      "id": "unique-id",
      "name": "Setlist Name",
      "songIds": ["id1", "id2"]
    }
  ]
}
```

## Key Implementation Details

- Songs displayed as animated floating bubbles in the Visualizer, grouped by Tag or Setlist
- Ultimate Guitar links use Universal Link domain for iPad app compatibility
- Export/Import functionality for data backup via JSON files
- Drag-and-drop reordering in setlists

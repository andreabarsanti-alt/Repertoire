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

- **Theme**: Deep blue-black / muted teal (`#3d9e8c`) studio/DAW aesthetic
- **Visualizer**: Animated glass bubbles packed with a circle-packing algorithm, grouped by tag; bubble size proportional to song count
- **Songs table**: Inline editing (click row or ✎ button); sortable columns; tag autocomplete dropdown on tags fields
- **Quick UG Import**: Parses title and artist from a UG URL slug — no network request needed. Pattern: `/tab/{artist}/{title}-{type}-{id}`
- **Ultimate Guitar links**: Normalized to `tabs.ultimate-guitar.com` Universal Link domain for iOS app compatibility
- **Setlists**: Drag-and-drop reordering via HTML5 drag API; touch-friendly ↑↓ buttons
- **Play mode**: Scroll-snap drum-roll UX — fixed 100px items, CSS `scroll-snap-type: y mandatory`, centered item detected via `scrollend` + debounced `scroll`. Auto-advances on return from UG via `visibilitychange`
- **GitHub push**: Uses GitHub Contents API (PUT) to commit `repertoire.json` directly; config stored in `localStorage`
- **Repo sync**: On boot, fetches `repertoire.json` and compares fingerprints; shows banner if remote differs
- **Viewport fix**: Resets viewport meta on `visibilitychange` to prevent iPad Safari zoom-out on app resume

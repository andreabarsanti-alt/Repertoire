# Repertoire

A single-page web app for managing a musical repertoire — built to run on an iPad via GitHub Pages, with no backend or build step required. Also usable on a phone: under 600px wide, the header folds its less-used controls behind a `⋯` button and the visualizer's bubbles get a bit more breathing room for their labels.

## Features

### Visualizer
- Songs displayed as animated floating glass bubbles, grouped by tag
- Bubble size scales with the number of songs in the group
- Click a bubble to open an overlay listing all songs in that group with Key / Capo / Harp badges
- Click a song title to open it in Ultimate Guitar

### Songs
- Sortable, searchable table of all songs
- Two filter dropdowns, alongside the text search: chord sheet (Any chords / **Has chords** / **No chords**) and repertoire scope (Any song / **In repertoire** / **🧪 Try out**)
- Check a song **🧪 Try out** right from its row — no need to type a tag — and it's automatically pulled into the **Try Outs** entry pinned at the top of the Setlists tab (and excluded from "In repertoire" above)
- Add, inline-edit, and delete songs
- Each song stores: Title, Tags, Key, Capo, Harp, Ultimate Guitar link
- Key / Capo / Harp shown as badge chips in all views
- **Quick UG Import** — paste a Ultimate Guitar URL to auto-detect title and artist
- **CRD reader** — paste in your own chord/lyric sheet per song (inline `[G]` chords or a chord line above the lyrics, `[Verse]`/`[Chorus]` section labels); if present, the title opens it in a full-screen in-app reader instead of Ultimate Guitar, with a UG link and edit still one tap away (no delete here — that's Songs-tab-only, so it's never a stray tap away mid-reading)
  - **Root key vs. Playing key** — type the key you're actually playing shapes in and the header live-shows the **Capo** you need (and re-transposes the chords on screen); edit the **Key** itself to preview a different root without touching the sheet. Nothing's saved until you tap **Save capo** (just remembers the capo) or **Update root** (also rewrites the sheet into the new key) — reload without tapping either and it's exactly as it was
  - Auto-defaults to the song's saved Capo/Key on open, and folds in the global **Half-step down tuning** toggle
  - Tap-to-hide header for a distraction-free view; keyboard scrolling (arrow keys / Page Up/Down)
  - **⤢ Full editor** — a full-screen, full-width view for comfortable long-sheet editing, with a **👁 Preview** button to swap in a live rendering (styled chords, section labels — the same look as the reader) when you want to check it. Just a Key field alongside it (no Playing key/Capo here) — it saves straight into the song when you tap Done, and it works even before the song's been saved. Also the go-to for fixing a sheet transcribed in the wrong key — edit the chords and Key together, right there
- Tag autocomplete when editing — suggests existing tags as you type

### Setlists
- Create and manage multiple setlists
- **Live vs. Generic** — the left pane splits setlists into two groups: **Live** (an actual gig, with a date — sorted soonest-first, e.g. "7 Aug 2026") and **Generic** (a working/rehearsal list, sorted alphabetically). Mark a setlist Live and give it a date from the same modal you name it in
- Two-column layout: setlist selector on the left, song list on the right
- Add songs, remove songs, reorder by drag-and-drop or ↑↓ buttons
- **✎ Edit right from the row** — same full edit form as the Songs tab (Title, Key, Capo, Harp, Artists, UG link, CRD), just without leaving the setlist
- **Alternate songs** — give any song slot a backup; swap which one is live with one tap
- **Sections** — collapsible chapter headings within a setlist (e.g. "Set 1", "Encore"); add songs directly under a section, collapse it to a single row, expand/collapse all with one tap. Adding a section always starts it empty — songs already after your selection stay right where they were, they don't get swallowed into the new section
- **Play mode** — scroll-snap drum-roll view (like iOS date picker): current song centered and large, others dimmed; auto-advances when returning from Ultimate Guitar
- **🧪 Try Outs** — a pinned entry above your setlists that auto-collects every song checked "Try out" — nothing to add or reorder by hand, just check the box on a song and it shows up here; uncheck it (from here or the Songs tab) and it drops back out

### Data
- **Half-step down tuning** — a global header toggle for instruments tuned down a half step; bumps every song's Capo by 1 fret (library-wide, confirmed) and shifts the CRD reader's transpose to match
- **Keyboard Mode** — a header toggle (device-local, not saved with the data) that relabels every "Capo" as "Transpose" throughout the app, for players who don't use a capo. Fret counts over 6 are shown wrapped an octave down as the equivalent negative shift (capo 7 → Transpose -5, capo 11 → Transpose -1) — the underlying saved value is unchanged, only the display
- All data stored in `localStorage` (persists across sessions on the same device)
- On first load, seeds from `repertoire.json` if no saved data exists
- Detects when the repo file differs from local data and offers to sync
- **🔄 Refresh** — hard-reloads both the app itself and its data from what's published (`repertoire_<profile>.json` and `index.html`), bypassing the browser cache on both — no GitHub login needed, replaces local data after a confirm
- **Export** — downloads current state as `repertoire.json`
- **Import** — loads a previously exported JSON file
- **Push to GitHub** — pushes `repertoire.json` directly to the repo via GitHub API
- **Profile switcher** — a header dropdown to jump between all available profiles (e.g. Two of Us / Loose Ends) without editing the URL by hand; its **✎ Edit profiles…** entry opens a popup to add a new one (starts empty — push to it once you've added songs) or delete one you no longer need

## Hosting on GitHub Pages

1. Push `index.html` and `repertoire.json` to the `main` branch
2. In the repo settings → **Pages** → set source to `main` / `root`
3. Access at `https://<username>.github.io/<repo>/`

The app works fully offline after the first load (localStorage persistence).

# Repertoire

A single-page web app for managing a musical repertoire — built to run on an iPad via GitHub Pages, with no backend or build step required. Export and Import live behind the header's `⋯` button. Also usable on a phone: under 600px wide, the header folds all its less-used controls behind `⋯` and the visualizer's bubbles get a bit more breathing room for their labels.

## Features

### Visualizer
- Songs displayed as animated floating glass bubbles, grouped by tag
- Bubble size scales with the number of songs in the group
- Click a bubble to open an overlay listing all songs in that group with Key / Capo / Harp badges
- Click a song title to open it in Ultimate Guitar

### Songs
- Sortable, searchable table of all songs
- Two filter dropdowns, alongside the text search: chord sheet (Any chords / **Has chords** / **No chords**) and repertoire scope (Any song / **In repertoire** / **🧪 Try out**)
- Check a song **🧪 Try out** right from its row — no need to type a tag — and it's excluded from "In repertoire" above until you uncheck it again
- Check **🎤 Lyrics only** (row, Add Song or edit) and the chord reader shows just the lyrics and section labels — the chords stay in the sheet, untouched
- Add, inline-edit, and delete songs
- Each song stores: Title, Tags, Key, Capo, Harp, Ultimate Guitar link
- Key / Capo / Harp shown as badge chips in all views
- **Quick UG Import** — paste a Ultimate Guitar URL to auto-detect title and artist; paste the chord sheet into the same dialog and **Add Song** saves it in one step, or **More details…** opens a fresh, pre-filled Add Song form (nothing carried over from a previous one) to set Key/Capo/Harp first
- **CRD reader** — paste in your own chord/lyric sheet per song (inline `[G]` chords or a chord line above the lyrics, `[Verse]`/`[Chorus]` section labels; any chord spelling is recognised — `7sus4`, `m7b5`, `maj9`, `add9`, `7M`, `G7(b9)`, slash bass `D/F#` and so on — and transposes cleanly); if present, the title opens it in a full-screen in-app reader instead of Ultimate Guitar, with a UG link and edit still one tap away (no delete here — that's Songs-tab-only, so it's never a stray tap away mid-reading)
  - **Tap a chord for its diagram** — a small popover shows how to play the chord as displayed: guitar fretboard normally, piano keyboard in Keyboard Mode, plus its notes. Tap anywhere (or Escape) to close; it doesn't affect auto-scroll or the pedal
  - **Root key vs. Playing key** — type the key you're actually playing shapes in and the header live-shows the **Capo** you need (and re-transposes the chords on screen); edit the **Key** itself to preview a different root without touching the sheet. Nothing's saved until you tap **Save capo** (just remembers the capo) or **Update root** (also rewrites the sheet into the new key) — reload without tapping either and it's exactly as it was
  - Auto-defaults to the song's saved Capo/Key on open, and folds in the global **Half-step down tuning** toggle
  - Tap-to-hide header for a distraction-free view; keyboard scrolling (arrow keys / Page Up/Down), with a **Scroll %** control for how far each press jumps
  - **Pedal Auto-scroll** — a header toggle (device-local) for a foot pedal that sends Up/Down keys. When on, the reader gets an **Auto** play/pause button (it keeps the same look either way, so the toolbar never shifts; the status pill bottom-right shows whether it is playing) with a speed level in steps of 5 (15 ≈ a full sheet in 3½ minutes), opening a song doesn't start scrolling until you tap play (sheet, Space or pedal), tapping the sheet or Space plays/pauses, and a status pill bottom-right shows/hides the toolbar. On the pedal: a short tap plays/pauses (Play resumes from where it paused), holding Down speeds auto-scroll up and holding Up slows it down by a step of 5 straight away and another every second while held, e.g. 15 → 20 → 25 (the new speed shows in the status pill; the toolbar −/+ also step by 5). Each song remembers its own speed automatically: whatever you last set while it was open is used next time (and syncs with Push/Pull); songs you haven't adjusted start at 20. Holds are recognised even on pedals that send a fast stream of repeated presses instead of a true held key. Touching the sheet pauses auto-scroll briefly, then it carries on. Off, the reader works exactly as before
  - **⤢ Full editor** — a full-screen, full-width view for comfortable long-sheet editing, with a **👁 Preview** button to swap in a live rendering (styled chords, section labels — the same look as the reader) when you want to check it. Just a Key field alongside it (no Playing key/Capo here) — it saves straight into the song when you tap Done, and it works even before the song's been saved. Also the go-to for fixing a sheet transcribed in the wrong key — edit the chords and Key together, right there
- Tag autocomplete when editing — suggests existing tags as you type

### Setlists
- Create and manage multiple setlists
- **⧉ Duplicate** a setlist — copies everything (sections, alternates, date) as "<name> (copy)"
- **Live vs. Generic** — the left pane splits setlists into two groups: **Live** (a gig, rehearsal or soundcheck, with a date — sorted soonest-first, shown e.g. "Rehearsal · 7 Aug 2026") and **Generic** (a working list, sorted alphabetically). Mark a setlist Live and pick its type and date from the same modal you name it in; older live setlists count as gigs
- **● Start live** — a header button that asks whether it's a gig, rehearsal or soundcheck, then notes down every song you open in the chord reader, in order (re-opening the song you just played isn't counted twice). A red **LIVE** pill shows the count (hidden while the chord reader is open), and recording carries on if you close the app. **■ Stop** saves the songs as a live setlist of that type, dated today, and pushes it
- Two-column layout: setlist selector on the left, song list on the right
- Add songs, remove songs, reorder by drag-and-drop or ↑↓ buttons
- **# Go to position** — the # button next to ↑↓ asks for a position number and moves the song straight there
- **+ Add all songs** or **+ Add artist** (pick an artist or tag) in the Add Songs window, to fill a big setlist in one go. Songs already in the setlist are skipped, and ticking "Hide Try outs" leaves try-outs out too
- **✎ Edit right from the row** — same full edit form as the Songs tab (Title, Key, Capo, Harp, Artists, UG link, CRD), just without leaving the setlist
- **Alternate songs** — give any song slot a backup; swap which one is live with one tap
- **Sections** — collapsible chapter headings within a setlist (e.g. "Set 1", "Encore"); add songs directly under a section, collapse it to a single row, expand/collapse all with one tap. Adding a section always starts it empty — songs already after your selection stay right where they were, they don't get swallowed into the new section
- **Play mode** — scroll-snap drum-roll view (like iOS date picker): current song centered and large, others dimmed; auto-advances when returning from Ultimate Guitar

### Live (audience requests)
- A **Live** tab for taking song requests from the audience at a gig. Pick one setlist (e.g. the big list of everything you might play) and **📣 Publish to audience**. Your audience scans a QR code (shown at the top left of the tab; **⤢ Show big** fills the screen, or print it) and gets a phone page in Italian, `request.html`, showing that setlist as big, easy-to-read tag bubbles (on a phone, compact colourful tag chips), with a search box to find a song directly. They tap a bubble, then a song, add an optional name and message, and send. **Something else?** lets them ask for any song as free text. No app or account is needed on their side
- Requests pop up in the Live tab in real time, with a chime, a toast and a red count on the tab button. **✓ Played** or **✕ Dismiss** each one; dismissed and played requests fold into a "Show done" list, with **↺** to undo
- The audience page shows a **Requested** board (song counts) and a **Played tonight** board. Names, messages and free-text requests are only ever shown to you. Tap ✓ next to a song in the Live tab's setlist to mark it played. During a **● Start live**, opening a published song in the chord reader marks it played automatically
- **Requests open** switch to pause and resume intake, **Unpublish** to take the page down, **Clear requests** before each gig. Each profile's audience link never changes, so a printed QR code keeps working
- Only the published songs' titles and tags leave the app, never chords, keys or other data, and they go to a free Firebase database, not GitHub. One-time setup (about 15 minutes): see [FIREBASE_SETUP.md](FIREBASE_SETUP.md). Until it's set up, the rest of the app works exactly as before

### Data
- **Half-step down tuning** — a global header toggle for instruments tuned down a half step; bumps every song's Capo by 1 fret (library-wide, confirmed) and shifts the CRD reader's transpose to match
- **Keyboard Mode** — a header toggle (device-local, not saved with the data) that relabels every "Capo" as "Transpose" throughout the app, for players who don't use a capo. Fret counts over 6 are shown wrapped an octave down as the equivalent negative shift (capo 7 → Transpose -5, capo 11 → Transpose -1) — the underlying saved value is unchanged, only the display
- All data stored in `localStorage` (persists across sessions on the same device)
- Each profile has its own data file in the repo, `repertoire_<profile>.json` (e.g. `repertoire_two_of_us.json`, `repertoire_loose_ends.json`), kept on a separate **`data` branch** so data pushes don't clutter the app's history on `main`
- On first load, seeds from the profile's file (read from the `data` branch through the GitHub API, no login needed) if no saved data exists
- Detects when the repo file differs from local data — comparing everything: every song field (chord sheet, Try out, Lyrics only, scroll speed…), setlists, tag colours and tuning — and offers to sync, saying how many songs/setlists differ
- **🔄 Refresh** — hard-reloads both the app itself and its data from the repo (`repertoire_<profile>.json` from the `data` branch, `index.html` from the published site), bypassing the browser cache on both — no GitHub login needed, replaces local data after a confirm
- **Export** — downloads current state as `repertoire_<profile>.json`
- **Import** — loads a previously exported JSON file
- **Push / Pull** — pushes the profile's `repertoire_<profile>.json` to the repo's `data` branch (or pulls it back) via the GitHub API
- **Profile switcher** — a header dropdown to jump between all available profiles (e.g. Two of Us / Loose Ends) without editing the URL by hand; its **✎ Edit profiles…** entry opens a popup to add a new one (starts empty — push to it once you've added songs) or delete one you no longer need

## Hosting on GitHub Pages

1. Push `index.html` (and `request.html`, the audience request page) to the `main` branch, and the `repertoire_<profile>.json` data files to a `data` branch (the app reads and writes them there via the GitHub API)
2. In the repo settings → **Pages** → set source to `main` / `root`
3. Access at `https://<username>.github.io/<repo>/`

The app works fully offline after the first load (localStorage persistence).

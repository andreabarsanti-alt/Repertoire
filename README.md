# Repertoire data

This branch holds only the app's data: one `repertoire_<profile>.json` per profile
(e.g. `repertoire_two_of_us.json`, `repertoire_loose_ends.json`).

The app itself lives on `main` (served by GitHub Pages). It reads and writes these files
through the GitHub Contents API (⬆ Push / ⬇ Pull / 🔄 Refresh / first-load seed / startup
repo check), so data pushes land here and never clutter `main`'s history.

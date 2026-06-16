# Interval — HIIT Timer ⚡

A tiny, offline-first PWA for HIIT / Tabata interval training, with a built-in workout log. No accounts, no servers — everything stays on your device.

## Features

- **Fully configurable intervals**: prepare, work, rest, rounds, sets, rest-between-sets, and cooldown
- **Presets**: built-ins (Tabata, Classic HIIT, 7-Minute, EMOM 10, Pyramid 2×8) plus your own saved presets
- **Big full-screen timer** with a color-coded phase ring, round/set counters, "next up", and elapsed/remaining totals
- **Audio cues** (Web Audio API): distinct tones when each phase begins + 3-2-1 countdown beeps — works offline, no audio files
- **Vibration** on every interval change (mobile)
- **Optional voice cues** ("Work" / "Rest") via the speech synthesizer
- **Keep-screen-awake** via the Screen Wake Lock API while a workout runs
- **Controls**: pause/resume, skip, previous, stop — plus keyboard shortcuts on desktop (Space, ←, →, Esc)
- **Workout log** with stats (total workouts, total working time, day streak, this week), per-entry detail, and delete
- **Export / import** JSON backups (presets + log; import merges)
- **Offline-first**: a service worker caches the whole app shell; data lives in `localStorage`
- Dark theme, safe-area insets, install-to-home-screen

## Files

```
hiit/
  index.html      app shell + all CSS/JS (no build step, no dependencies)
  manifest.json   PWA manifest (standalone, theme color, icons)
  sw.js           cache-first service worker
  icon-192.png    app icon
  icon-512.png    app icon (also used as maskable)
  README.md       this file
```

## Running it

It's a static site — any static host works. Service workers require **HTTPS or localhost**, so don't open `index.html` via `file://` if you want install/offline support.

```bash
cd interval
python -m http.server 8000
# then visit http://localhost:8000
```

(Or `npx serve`, etc.)

### GitHub Pages

All asset paths are relative, so the app works whether it's served from a
domain root or a project subpath like `https://<user>.github.io/Interval/`.
A workflow at `.github/workflows/deploy.yml` publishes the site automatically:

1. In the repo, go to **Settings → Pages → Build and deployment**.
2. Set **Source** to **GitHub Actions**.
3. Push to the default branch (`main`/`master`) — the workflow builds and
   deploys, and the live URL appears in the Actions run summary.

The included `.nojekyll` file disables Jekyll processing so all files are
served as-is.

## Install to home screen

- **Android (Chrome)**: open the URL → **⋮** menu → **Add to Home screen** / **Install app**.
- **iOS (Safari)**: open the URL in Safari → **Share** → **Add to Home Screen**.

After the first load, the app works fully offline.

## Backups

Your data only exists on the device/browser where you logged it. Use **Log → Export** to download a JSON file, and **Import** to restore it (merges by entry id). The exported file looks like:

```json
{
  "app": "interval",
  "version": 1,
  "exportedAt": "2026-06-16T12:00:00.000Z",
  "presets": [{ "name": "My Tabata", "cfg": { "work": 20, "rest": 10, "rounds": 8, "sets": 1 } }],
  "log": [
    { "id": "abc123", "ts": 1781000000000, "name": "Tabata", "durSec": 240, "workSec": 160, "rounds": 8, "completed": true }
  ]
}
```

## Notes

- Audio and wake lock are enabled on first interaction (the **Start** tap) to satisfy browser autoplay/gesture policies.
- A workout is logged when it finishes, or when stopped early after at least one work interval (marked **partial**).

# CLAUDE.md

Guidance for Claude Code (and other agents) working in this repo.

## Project overview

**Bass Buddy** is a bass fishing helper for a parent learning to fish with their kid. It has two
jobs:

1. **Recommendation engine** — from location, weather, water type, gear, and the angler's
   age/experience, it produces a bite-forecast rating plus when/where to fish, what lure to throw,
   and an age-appropriate strategy.
2. **Fish-Dex** — a Pokédex-style catch log: species likely in your area start as greyed-out
   silhouettes and unlock as you log catches, with progress, badges, and a JSON export.

Audience is beginners, so **keep user-facing copy plain, encouraging, and jargon-light**.

## Architecture — read this first

The entire app is **one self-contained file: `index.html`** (inline HTML + CSS + JS).

- **No build step, no dependencies, no framework, no server, no bundler.** Just open the file.
- All persistent state lives in `localStorage` under the key **`bassbuddy`** (profile, gear,
  catch log with optional downscaled base64 photos).
- Keep it single-file and dependency-free **unless the user explicitly decides otherwise.** That
  constraint is the point — it must open on a phone at the water with no install.

## Code map (all inside `index.html`)

- **Data constants:** `FISH` (species catalog), `GEAR_RODS`, `GEAR_LURES`.
- **State:** `DEFAULT_STATE`, the live `S` object, `load()`, `persist()`, `saveState()`, `val()`.
- **Location & weather:** `searchPlace()`, `useMyLocation()`, `fetchWeather()`, `manualWeather()`,
  `REGION_BY_STATE` (maps a US state → one of `south|northeast|midwest|west`).
- **Recommendation engine:** `seasonFor()`, `phaseFromTemp()`, `estWaterTemp()`, `biteScore()`,
  `timeWindows()`, `whereToFish()`, `lureAdvice()`, `ageStrategy()`, orchestrated by `planTrip()`.
- **Fish-Dex:** `areaFish()`, `renderDex()`, `renderBadges()`, `renderLogList()`, `showFish()`,
  `openLog()`/`saveCatch()`/`deleteCatch()`, `previewPhoto()` (downscales to keep localStorage
  small), `exportLog()`.
- **UI plumbing:** `showTab()`, `toast()`, `initUI()`, theme toggle via `data-theme`.

## External data (all free, no API key)

- **Open-Meteo Forecast** — current weather + hourly `surface_pressure` (used to derive the
  rising/steady/falling pressure trend).
- **Open-Meteo Geocoding** — place name → coordinates.
- **Browser Geolocation** + **BigDataCloud reverse geocoding** — "use my location" → state/region.
- **Offline fallback:** the "enter conditions manually" checkbox runs the full engine with no
  network (`manualWeather()`).

Note: in the Claude Code build sandbox these API hosts are blocked by the egress proxy, so live
calls can't be exercised here. Validate API changes against each service's documented request/
response contract and test the manual path.

## Run & verify

- **Run:** open `index.html` in any modern browser (or serve it statically). On a phone, save the
  file and use "Add to Home Screen".
- **Verify:** there is no in-repo test runner or `package.json` by design. The repeatable check is a
  headless-Chromium (Playwright) pass. This environment has Chromium preinstalled; a session can:
  - `npm i playwright-core` in a scratch dir, then launch with
    `executablePath: '/opt/pw-browsers/chromium-1194/chrome-linux/chrome'` and `args: ['--no-sandbox']`,
    load the `file://` URL, and assert: no console/page errors; the engine's score responds to
    inputs (prime low-light/overcast/falling-pressure scores high, midday/clear/rising scores low);
    age changes shift the strategy text; logging a catch unlocks its card and awards badges; state
    survives a reload; `areaFish()` re-scopes when region/water change.

## Conventions

- Vanilla JS in the existing terse style — small helpers like `val()`, `escapeHtml()`, `cap()`, and
  in-place array toggles (`toggleArr`). Match it; don't introduce a framework.
- CSS is mobile-first and theme-aware via `@media (prefers-color-scheme)` plus `:root[data-theme]`
  overrides for the manual toggle. Style both light and dark when adding UI.
- **Adding a species:** append to `FISH` with `regions` (subset of the four US regions),
  `waters` (`pond|lake|river`), and `cat` (`bass|panfish|catfish|pike|trout|other|rough`). Those
  tags drive both the Fish-Dex area scoping and badge logic — don't skip them.
- Always keep the **fishing-license/regulations reminder and life-jacket/safety** guidance in
  user-facing output.

## Git workflow

- Active branch: **`claude/bass-fishing-strategy-jzd8re`** — commit and push there.
- Do **not** open a pull request unless the user explicitly asks.

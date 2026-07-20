# 🎣 Bass Buddy

**▶️ Live app: https://erictlin606.github.io/BassBuddy/** — open it on your phone and
"Add to Home Screen" to use it like an app at the water.

A single-page bass fishing coach **and** a Pokédex-style catch log ("Fish-Dex"), built for
a parent learning to fish with their kid. Open it on your phone at the water — no app store,
no account, no server.

Everything runs in one self-contained `index.html`. Your setup and catch log are saved in the
browser on your device (nothing is uploaded anywhere).

---

## What it does

### 🧭 Plan a Trip
You tell it **where** (search a place, tap 📍 for your location, or type conditions by hand) and
**when**, plus your **water type** (pond / lake / river). It then gives you:

- **Bite forecast** — a 0–100 rating with the reasons behind it (time of day, cloud cover, wind,
  barometric-pressure trend, season/water-temp phase).
- **When to fish** — the best time windows for today's conditions and season.
- **Where to cast** — structure and cover to target, tailored to your water type and the season.
- **What to throw** — lure/bait picks chosen from **the gear you actually own**, plus a couple of
  smart "worth picking up" suggestions when a top pick is missing.
- **Strategy for your angler** — advice that scales with your child's **age and experience**, from
  "bobber-and-a-worm for guaranteed bluegill" up to "pattern the fish and chase a personal best."
- **Before you go** — a license/regulations reminder with a link to look up your state's rules.

### 📖 Fish-Dex (the game)
A collection grid of every species you're likely to catch **in your area**. Each starts as a
greyed-out silhouette; **log a catch to unlock its card** in full color. Track progress toward
"catch 'em all," earn badges (First Catch, First Bass, Panfish Master, Personal Best, Local
Legend…), keep a photo catch log, and export it to JSON so it's never lost.

### ⚙️ Setup
Your angler's name/age/experience, home region, water type, and gear inventory — all of which feed
the recommendations. Update the age/experience over time and the advice grows up with your kid.

---

## How to use it

1. Open `index.html` in any modern browser (double-click it, or host it anywhere static).
2. **On your phone:** email yourself the file or drop it in a cloud drive and open it in the
   browser — then use "Add to Home Screen" so it opens like an app.
3. First visit: pop into **⚙️ Setup** to set your angler's age and check off the gear you own.

No installation, no build step, no dependencies.

---

## Public data it uses (all free, no API key)

| Purpose | Service |
| --- | --- |
| Weather + **barometric pressure trend** | [Open-Meteo Forecast API](https://open-meteo.com) |
| Search a place name → coordinates | [Open-Meteo Geocoding API](https://open-meteo.com) |
| "Use my location" → state/region | Browser Geolocation + [BigDataCloud reverse geocoding](https://www.bigdatacloud.com) |

**Offline?** Tick **"No internet? Enter conditions manually"** on the Plan tab and the full
recommendation engine still runs — handy at a remote lake with no signal.

---

## How the advice is reasoned (the short version)

- **Pressure:** falling pressure ahead of a front = feeding window; rising pressure after a front =
  finicky, go finesse.
- **Light/sky:** low light (dawn/dusk/overcast) = bass roam and feed; bright bluebird sky = they
  tuck into cover.
- **Wind:** a light chop positions feeders and is good; heavy wind pushes you to protected banks.
- **Season/water temp:** cold = slow & deep; pre-spawn/spawn = shallow & aggressive; summer =
  early/late shallow, midday deep/shade; fall = chasing bait.
- **Lures** are filtered to what you own and matched to the day's conditions, with beginner-friendly
  buy suggestions when a top pick is missing.

This is a rules-of-thumb coach to tilt the odds and help you both *learn why* — not a guarantee. Fish
are still fish. 🐟

---

## Privacy & safety

- All data (profile, gear, catch log, photos) is stored **only in this browser** via `localStorage`.
  Nothing is sent to any server. Use **Reset all data** in Setup to wipe it.
- Always carry a valid fishing license, follow local size/bag limits, and wear a life jacket around
  water.

---

## Development notes

- Single file: `index.html` (HTML + CSS + JS inline, no dependencies, no build).
- Verified end-to-end with a headless-Chromium (Playwright) pass covering load/no-errors, the
  recommendation engine responding to inputs, age-based strategy changes, catch logging + badges +
  card unlocks, `localStorage` persistence across reload, and area re-scoping.
- The three public APIs above couldn't be exercised from the build sandbox (its egress proxy blocks
  those hosts), but the request URLs and response fields follow each API's documented contract and
  work when the page is opened on a normal network. The manual-entry path is fully tested offline.

Tight lines! 🎣

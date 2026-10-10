# 🎬 Lúmen Player (`rkd-player-module`)

<p align="center">
  <img src="https://img.shields.io/badge/platform-Electron-47848F?style=for-the-badge&logo=electron&logoColor=white" alt="Electron">
  <img src="https://img.shields.io/badge/runtime-Node.js-5FA04E?style=for-the-badge&logo=nodedotjs&logoColor=white" alt="Node.js">
  <img src="https://img.shields.io/badge/language-JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black" alt="JavaScript">
  <img src="https://img.shields.io/badge/markup-HTML-E34F26?style=for-the-badge&logo=html5&logoColor=white" alt="HTML">
  <img src="https://img.shields.io/badge/style-CSS-1572B6?style=for-the-badge&logo=css3&logoColor=white" alt="CSS">
  <img src="https://img.shields.io/badge/api-YouTube-FF0000?style=for-the-badge&logo=youtube&logoColor=white" alt="YouTube IFrame Player API">
  <img src="https://img.shields.io/badge/testing-Playwright-2EAD33?style=for-the-badge&logo=playwright&logoColor=white" alt="Playwright">
  <img src="https://img.shields.io/badge/format-JSON-000000?style=for-the-badge&logo=json&logoColor=white" alt="JSON">
  <img src="https://img.shields.io/badge/tool-Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white" alt="Docker">
</p>

## ✨ About the project

**Lúmen Player** is a YouTube player for desktop (Electron) and web browsers, with a Brazilian Portuguese interface and the **Lúmen** visual identity. The npm package is named `rkd-player-module` (version 1.1.0).

The main flow is Cinema: you save channels in a modal library, pick a channel to fill a horizontal carousel with public videos (no autoplay), and start playback by clicking a thumbnail. There are three dark themes (Cyberpunk, Radar and Space Station), reactive audio visualizers, a local history of the last 100 watched videos, and JSON storage on disk — no external database and no YouTube Data API key.

The code is licensed under **MIT** (see `LICENSE`). City names and coordinates in the Radar theme come from GeoNames (CC BY 4.0); details in [`RADAR-DATA-NOTICES.md`](RADAR-DATA-NOTICES.md).

## 🛠️ Technologies used

| Technology | What it is used for |
| --- | --- |
| **JavaScript (CommonJS and UI modules)** | App logic, IPC, local servers and the user interface |
| **HTML and CSS** | Cinema layout, dialogs, themes and styles |
| **Node.js ≥ 22.12.0** | Runtime required by `package.json` (`engines.node`) |
| **Electron 44.3.0** | Desktop window, restricted IPC, native opacity and capture of the window's own audio |
| **Playwright 1.63.0** | Development dependency for Electron/browser tests |
| **Node.js test runner** (`node --test`) | Unit tests in `tests/*.test.cjs` |
| **YouTube IFrame Player API** | Embedded playback (`player.js` / `space-player.js`) |
| **Web Audio API + Canvas 2D** | Audio analysis and drawing of the visualizers |
| **Native Node.js HTTP** | Local Electron server (`server.cjs`) and web version (`web-server.cjs`) |
| **JSON on disk** | Persistence of library, history and preferences (`data/library.json`) |
| **OpenSky Network API** | Aircraft positions in the Radar theme (anonymous access; no key in the code) |
| **Natural Earth / GeoNames** | Base map (`radar-map.json`) and cities (`radar-cities.json`) |
| **Docker (`Dockerfile.web`)** | Node Alpine image (`node:22-alpine`) for the web version only |

There is no UI framework (React, Vue, etc.), no frontend bundler and no installer/packaging defined in the repository — the app runs from source with `npm`.

## 🚀 Mapped features

Status legend: **COMPLETE** = implemented, covered by adequate tests, and those tests pass. **PARTIAL** = implemented halfway, without enough tests, or covered by tests that fail or cannot run offline.

| Feature | What it does | Status |
| --- | --- | --- |
| Channel library | Saves channel links (`@handle`, `/channel/`, `/c/`, `/user/`), resolves the canonical ID, searches, removes, shows avatar/banner/subscribers, and limits the library to 2,000 channels | COMPLETE |
| Legacy library migration | Converts old video records into unique channels; keeps failures in `legacyVideos` and offers "retry conversion" | COMPLETE |
| Recommendations and carousel | Fetches public channel videos (feed/page), caches for ~5 min / up to 360 candidates, draws up to 16 thumbnails, no autoplay when only selecting a channel | COMPLETE |
| YouTube playback (Cinema) | Isolated embedded player (separate loopback origin), native YouTube controls, selection from the carousel | COMPLETE |
| Playback history | Stores up to 100 watched videos (thumbnail, title, date); click/keyboard replays and reloads the channel carousel | COMPLETE |
| Local likes | Like button in the player marks history entries; does not send a like to the YouTube account | COMPLETE |
| Audio visualizer | Six styles (Bars, Waves, Aurora, DNA, Constellation, LED Matrix) in the title bar, capturing the window/tab's own audio | COMPLETE |
| Cyberpunk background | Slideshow of history covers / current video cover in the Cyberpunk theme modals | COMPLETE |
| Web version (HTTP + API) | `web-server.cjs` server, `web-bridge.js` bridge, `/health`, server-side persistence and browser integration | COMPLETE |
| JSON persistence | Reads/writes `library.json` with atomic writes and a backup if the file is unreadable; `data/`, `test-results/` and `work/` are git-ignored | COMPLETE |
| Radar city names | Local labels from `radar-cities.json` (GeoNames), without overlap on the canvas | COMPLETE |
| UI themes | Three themes (Cyberpunk, Radar, Space Station) with video color presets, visualizer and transparency | PARTIAL |
| Video color overlay | ~16% opacity overlay (yellow, blue, red, pink or none) | PARTIAL |
| Window transparency | 0%–65% slider using Electron's native opacity (hidden on web) | PARTIAL |
| Keep playing when minimized | Desktop preference to keep or pause playback when the window is minimized | PARTIAL |
| Radar background (live OpenSky) | Map with real aircraft (~60 s refresh), default SP/RJ region or centered on the device location; canvas shared with modals | PARTIAL |
| Device location (Radar) | On Windows desktop it uses PowerShell; on the web it uses the browser `geolocation`; coordinates stay in memory only | PARTIAL |
| Space Station background (ISS) | Muted ISS livestream behind the UI; moves to modals without restarting | PARTIAL |

### Test coverage summary

| Area | Tests that cover it |
| --- | --- |
| URLs, settings, store, channels | `npm test` → `tests/core.test.cjs` |
| History and likes | `tests/history.test.cjs`; Electron: `test:history`, `test:history-playback` |
| Legacy migration | `tests/migration.test.cjs`; also exercised in `test:recommendations` |
| Recommendations / catalog | `tests/recommendations.test.cjs`, `tests/recommendation-catalog.test.cjs`; Electron: `test:recommendations`, `test:channels` |
| Radar (logic) | `tests/radar.test.cjs`, `tests/location.test.cjs` |
| Cyberpunk background | `tests/cyberpunk-background.test.cjs`; `test:cyberpunk-background` |
| Radar map cities | `test:radar-cities` |
| Web server | `tests/web-server.test.cjs`; `test:web` |
| Live visualizer | `test:visualizer` |
| Themes / preferences | `test:app` — fails in the last verification (an assertion in `scripts/test-app.cjs` line 65: expected 1, got 2) |
| Live OpenSky / Windows location / minimize | `test:radar`, `test:radar-location`, `test:youtube` — need real network or Windows and were not verified |

## ▶️ How to run the project

### Requirements

1. **Node.js 22.12.0 or newer** and **npm** (`package.json` declares `"engines": { "node": ">=22.12.0" }`).
2. **Internet access** to install dependencies and to load YouTube, metadata, thumbnails and (in the Radar theme) the OpenSky API.
3. **Desktop (Electron):** a system where Electron 44 can run. On headless Linux, a virtual display (`xvfb-run`) is usually needed to open the window.
4. **Web:** only Node.js (Electron is not needed if you only use `start:web`).
5. **Docker (optional):** to build the image defined in `Dockerfile.web`.

### Install

From the project root (where `package.json` is):

```bash
npm ci
```

`npm ci` installs the versions pinned in `package-lock.json` (Electron and Playwright as `devDependencies`). `npm install` also works, but `npm ci` is the reproducible path.

### Desktop version (Electron)

```bash
npm start
```

This runs `node scripts/start.cjs`, which launches Electron with the app. Local servers bind to `127.0.0.1` on free ports. Data is stored in `data/` by default (Electron session in `data/session/`).

To keep the data in another folder:

```bash
export LUMEN_DATA_DIR="$PWD/work/demo-data"
npm start
```

On Windows, the launcher sets `NODE_USE_SYSTEM_CA=1` when it is not already set (trusting the system certificate store).

### Web version (browser)

```bash
npm run start:web
```

Open `http://localhost:3000` (defaults: host `127.0.0.1`, port `3000`). The library is stored in `data/library.json` on the server.

Optional environment variables:

| Variable | Default | Purpose |
| --- | --- | --- |
| `PORT` | `3000` | HTTP port |
| `LUMEN_HOST` | `127.0.0.1` | Bind address |
| `LUMEN_DATA_DIR` | `<project>/data` | Data directory |
| `LUMEN_ALLOWED_HOSTS` | `localhost,127.0.0.1` | Accepted HTTP hosts (comma-separated list) |

Example on another port:

```bash
PORT=3001 npm run start:web
```

On the web, native transparency and "keep playing when minimized" are disabled/forced by the server; the visualizer depends on sharing the tab with audio; Radar geolocation asks for browser permission in a secure context (`localhost` or HTTPS). The web API has no login — restrict the port to trusted networks/users.

### Container (web only)

`Dockerfile.web` copies only `src/`, uses `node:22-alpine`, sets `PORT=3000`, `LUMEN_HOST=0.0.0.0` and `LUMEN_DATA_DIR=/data`, and starts `node src/web-server.cjs`. The suggested volume is `/data` (the `library.json` file). The `/health` endpoint answers basic readiness.

```bash
docker build -f Dockerfile.web -t lumen-web .
docker run --rm -p 8082:3000 -v lumen_player_data:/data lumen-web
```

Then open `http://localhost:8082`. If you publish the port under a host name other than `localhost`/`127.0.0.1`, set `LUMEN_ALLOWED_HOSTS` to include the name you use.

### Relevant files and folders

| Path | Role |
| --- | --- |
| `data/library.json` | Channels, history, settings and `legacyVideos` (created at runtime; not versioned) |
| `LUMEN_DATA_DIR` / `LUMEN_TEST_DIR` | Override app data and test output/isolation |
| `RADAR-DATA-NOTICES.md` | GeoNames attribution and regeneration via `scripts/import-radar-cities.cjs` |

Nothing else needs to be edited to start the player; there is no mandatory `.env` file in the repository.

### Tests (reference)

```bash
npm test                          # unit tests (node --test)
npm run test:web                  # browser integration (Playwright, Edge)
npm run test:channels             # Electron — channel switching
npm run test:recommendations      # Electron — carousel / playback
npm run test:history              # Electron — history
npm run test:history-playback     # Electron — replay from history
npm run test:visualizer           # Electron — audio capture
npm run test:cyberpunk-background # Chromium — Cyberpunk background
npm run test:radar-cities         # Chromium — city labels
npm run test:app                  # Electron — library / themes / persistence
npm run test:radar                # Electron — live OpenSky
npm run test:radar-location       # Electron — location (Windows focus)
npm run test:youtube              # Electron — playback + minimize
```

Electron tests usually need a display (for example `xvfb-run -a npm run test:channels` on headless Linux) and, in many cases, real network access.

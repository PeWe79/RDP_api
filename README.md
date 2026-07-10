# Radio Player HTML5 — Single or Multi-Station, 3 Layouts, PWA

A modern HTML5 radio player for Icecast / Shoutcast / Zeno.FM / Azuracast (and any stream with metadata), with real-time "now playing" info, song history, album art, lyrics, weekly program schedule, TV modal and Progressive Web App support.

It ships with **three ready-to-use layouts ("versions")** sharing the same engine — you pick the one you want in a single config file.

## Demo Screenshots

![Demo Screenshot](https://i.imgur.com/oULEMgZ.jpeg)

## Layouts / Versions

| id | File | Description |
|---|---|---|
| `retro` | `index-redesign.html` | **Retrô Glass** — new redesign: frosted glass, dynamic accent color extracted from the album art |
| `classic` | `index-classic.html` | **Clássico** — the original player look |
| `aurora` | `index-alt.html` | **Aurora Deck** — alternative deck-style UI |

All three read the same `config.js` and the same `js/main.js`, so switching layout never means reconfiguring your stations.

### How to choose your version

Everything is set in **`config.js`**:

```javascript
window.streams = {
    layout: "retro",        // "retro" | "classic" | "aurora"
    timeRefresh: 10000,
    stations: [ /* ... */ ],
};
```

* **`layout`** — which design `index.html` loads. The aliases `classico`, `redesign` and `alt` are also accepted.
* **Direct file:** you can also deploy/open `index-redesign.html`, `index-classic.html` or `index-alt.html` directly — each one works standalone.

## Features

* Current song, artist and album art, updated in real time (free metadata API included — no key required).
* Song history with cover art.
* Song lyrics via [lyrics.ovh](https://lyrics.ovh) with [LRCLIB](https://lrclib.net) fallback — no API key needed.
* Weekly program schedule (`programSchedule`) with an automatic "on air now" indicator, plus a full schedule panel.
* Dynamic accent color extracted from the current album art (Color Thief).
* Live TV / video stream modal (`tv_url`), with the radio correctly paused while TV plays.
* Media Session integration (lock-screen / notification controls with artwork).
* Multiple stations in one player, with a station list and next/previous switching.
* Audio visualizer, social links, app download links.
* Responsive design + installable PWA.
* Visual config generator: open **`gerador.html`** in your browser, fill in your stations and copy the ready-made `config.js`.

## Quick Start

1. Clone or download this repository.
2. Edit **`config.js`** with your station(s) — or open **`gerador.html`** in a browser, fill the form and paste the generated result into `config.js`.
3. Replace the images in `assets/` (logo, default cover, favicon) with your own.
4. Upload everything to any static host — no PHP or build step required.

```javascript
// config.js — minimal example
window.streams = {
    layout: "retro",
    timeRefresh: 10000,
    stations: [
        {
            name: "Example FM",
            hash: "examplefm",
            description: "The best music!",
            logo: "assets/logo.png",
            album: "assets/cover.png",
            cover: "assets/cover.png",
            api: "",                                    // empty = use the free web API
            stream_url: "https://example.com/stream",
            server: "itunes",                           // "itunes" or "spotify" for extra art lookup
            social: { instagram: "https://instagram.com/examplefm" },
        },
    ],
};
```

## Station Configuration Reference

| Property | Required | Description |
|---|---|---|
| `name` | ✔ | Station name shown in the interface. |
| `hash` | ✔ | Unique identifier for the station. |
| `description` | ✔ | Short description / slogan. |
| `logo` | ✔ | Path/URL of the station logo. |
| `album` | ✔ | Default cover shown before the real album art loads. |
| `cover` | ✔ | Fallback cover for the current song. |
| `stream_url` | ✔ | Audio stream URL. |
| `frequency` | | Real FM frequency shown on the **retro** layout dial (e.g. `"87.9"`; comma also accepted). If omitted, a frequency is assigned automatically across the band. |
| `api` | | Custom metadata endpoint. Leave `""` to use the free built-in web API (`api.twj.es`). |
| `server` | | `"spotify"` or `"itunes"` — extra source for album art lookup. |
| `tv_url` | | Live video stream URL — enables the TV button/modal. |
| `program` | | Fixed "on air" info: `{ time, name, description }`. Used when there is no `programSchedule`. |
| `programSchedule` | | Weekly schedule (see below). Enables the "Programação" panel and the automatic "on air now" display. |
| `social` | | Links: `facebook`, `instagram`, `twitter`, `whatsapp`, `tiktok`, `youtube`. |
| `apps` | | App links: `android`, `ios`. |

### Weekly schedule format

```javascript
programSchedule: [
    // days: "dom", "seg", "ter", "qua", "qui", "sex", "sab"
    { days: ["seg","ter","qua","qui","sex"], start: "06:00", end: "12:00", name: "Morning Show", description: "With John Doe" },
    { days: ["dom","sab"],                   start: "18:00", end: "00:00", name: "Weekend Party", description: "Non stop music" },
],
```

The player highlights the slot that is on air right now and lists the full week in the schedule panel.

## File Structure

* **`config.js`** — all your settings (layout choice + stations). The only file you need to edit.
* **`index.html`** — loader: reads `config.js` and shows the chosen layout.
* **`index-redesign.html` / `index-classic.html` / `index-alt.html`** — the three layouts (each also works standalone).
* **`gerador.html`** — visual generator for `config.js` (runs locally in your browser).
* **`js/main.js`** — the shared player engine (audio, metadata polling/SSE, lyrics, history, schedule, media session).
* **`css/main.min.css`, `custom.css`, `rp-redesign.css`, `css/alt.css`** — styles per layout.
* **`assets/`** — images and icons.

## Free Hosting

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/jailsonsb2/Radioplayer_api)
[![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/jailsonsb2/Radioplayer_api)

## Radio Metadata API

Get real-time metadata from online radio streams — free, no key required. Leave the station's `api` field empty to use it automatically.

### Available Endpoints

Base URL: `https://api.twj.es`

* `/` **(GET):** Current song and recent history as JSON (`songtitle`, `artist`, `song`, `source`, `song_history`). Shared 5-second cache — safe to poll. This is what the player uses by default.
    - **Required parameter:** `url` — the URL of the radio stream.
* `/stream.php` **(GET, SSE):** Same data pushed in real time via Server-Sent Events — connect with `EventSource` instead of polling.
    - **Required parameter:** `url` — the URL of the radio stream.
* `/search.php` **(GET):** Album art lookup for a song.
    - **Required parameter:** `query` — e.g. `Artist - Song`.

**Example:** `https://api.twj.es/?url=https://example.com/stream`

## Contributing

1. Fork the project.
2. Create a branch for your feature (`git checkout -b feature/new-feature`).
3. Commit your changes (`git commit -am 'Add new feature'`).
4. Push to the branch (`git push origin feature/new-feature`).
5. Create a new Pull Request.

---

## 📜 History

First published in mid-2024, this project descends from one of the first open-source "now playing" metadata APIs for web radio players written in plain PHP ([RadioplayerAPI](https://github.com/jailsonsb2/RadioplayerAPI), June 2024). The response format it introduced — `songtitle`, `artist`, `song`, `source`, `song_history` — has since been widely adopted across the web radio ecosystem, including by third-party commercial products.

---

## ⚖️ License

This project is licensed under the **GNU AGPL-3.0** (see [LICENSE](LICENSE)): you are free to use, modify and redistribute it — including commercially — provided derivative works remain open source and keep the original copyright notices, **even when offered only as a hosted/network service**.

**Closed-source / commercial licensing:** to embed this code in a proprietary product without AGPL obligations, a separate commercial license is available — contact [contato@jailson.es](mailto:contato@jailson.es).

Copyright (C) 2024-2026 Jailson Bezerra ([@jailsonsb2](https://github.com/jailsonsb2))

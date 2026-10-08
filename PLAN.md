# LH Cycle Training – Project Plan

A fully open-source indoor cycling app, created as an **independent fork** of [Incyclist](https://github.com/incyclist), with:

1. A new, significantly better-looking user interface (guided by SVG sketches)
2. A local video library (file import + optional local video server)
3. A video pairing wizard (video + GPX/FIT, and GoPro videos with embedded GPS)
4. Training history in SQLite with an analysis overview

- **Product name:** LH Cycle Training (name check done – no existing product with this name)
- **Repository:** https://github.com/shenphenchoedron-hue/LH-Cycle-Training (public, default branch `main`)
- **Licence:** MIT (`LICENSE`, © 2026 Lone Hansen and LH Cycle Training contributors)
- **First release platforms:** Windows, macOS, Linux (desktop, Electron). Mobile and Apple TV: see Phase 11.

---

## Guiding principles

- **Fully independent.** The code of Incyclist is imported **once**, as a snapshot. After that, LH Cycle Training lives on its own: no upstream remotes, no sync jobs, no Incyclist npm packages, no Incyclist servers, no Incyclist update channel.
- **Own everything.** All Incyclist code the app depends on (UI, desktop shell, services, devices, event logger) is part of this repository and maintained here.
- **Respect the origin.** Incyclist is MIT-licensed: its copyright and licence notices are kept, and Incyclist is credited in the README and About dialog. All licence obligations are tracked in `docs/licensing/LICENSING.md`.
- **No ANT+.** ANT+ support is removed from the app (it often causes trainer connection problems). Connections: Bluetooth (BLE), Wi-Fi Direct Connect, and serial (Daum/Kettler ergo bikes).
- **Fully open source.** All code and dependencies under OSI-approved licences (MIT / Apache 2.0 / BSD / LGPL / public domain). No secrets in the repository.
- **Your taste leads the design.** SVG sketches in `design/sketches/` show the desired look; they guide the redesign without being a strict specification.

---

## Repository layout (target)

One repository (monorepo) with npm workspaces:

```
LH-Cycle-Training/
├── apps/
│   ├── web-ui/          from incyclist/web-ui    – user interface (React)
│   └── desktop/         from incyclist/desktop   – Electron shell, native bindings (BLE, serial, ffmpeg, SQLite, GPMF)
├── packages/
│   ├── services/        from incyclist/services  – business logic (routes, rides, workouts, activities, settings)
│   ├── devices/         from incyclist/devices   – trainer/sensor protocols (BLE, serial, Direct Connect) – ANT+ code removed
│   ├── eventlog/        from gdoumen/EventLogger (npm: gd-eventlog) – logging library
│   └── addons/          new – pairing wizard logic, GoPro import, analysis engine, SQLite data layer
├── tools/
│   └── video-server/    new – optional local video library server
├── design/sketches/     your SVG sketches as visual guidelines + README (provenance, AI-generated content)
├── docs/                user + developer documentation, website (GitHub Pages)
│   └── licensing/       LICENSING.md (compliance guide + release checklist), author-license-request.md
├── .github/             CI workflows, issue/PR templates
├── LICENSE              MIT
├── THIRD_PARTY_NOTICES.md   Incyclist + EventLogger MIT notices, fonts, images, map data, FFmpeg, dependency licences
├── PLAN.md              this plan
└── package.json         npm workspaces root
```

Package names inside the workspace (private, not published to npm):

| Original npm package | New workspace package |
|----------------------|-----------------------|
| `incyclist-services` | `@lh-cycle-training/services` |
| `incyclist-devices` | `@lh-cycle-training/devices` |
| `gd-eventlog` | `@lh-cycle-training/eventlog` |
| – (new) | `@lh-cycle-training/addons` |

---

## Phase 0 – Decisions and prerequisites

**Goal:** remove legal, naming and account blockers before writing code.

| # | Task | Status / output |
|---|------|-----------------|
| 0.1 | Product name and name check | **Done:** "LH Cycle Training" |
| 0.2 | Choose the licence and add `LICENSE` to the repository | **Done: MIT** |
| 0.3 | Ask the Incyclist author to confirm MIT (and add LICENSE files) for `incyclist/desktop` **and** `gd-eventlog` – both only declare MIT in `package.json`. **Sent 2026-10-08:** https://github.com/incyclist/desktop/issues/222 – answer pending | Written confirmation stored in `docs/licensing/` |
| 0.4 | Configure the repository: issue labels, project board, branch protection on `main`, topics | Configured repo |
| 0.5 | Website and update feed: GitHub Pages + GitHub Releases | URLs |
| 0.6 | Accounts: Apple Developer Program (macOS signing + notarisation), Windows code-signing certificate, Strava API application, optional Komoot / intervals.icu API access. Credentials only in a password manager / CI secrets, **never** in git | Accounts |
| 0.7 | Hardware test set: at least one BLE FTMS trainer, one BLE power meter, one BLE heart-rate strap; optional Direct Connect (Wi-Fi) trainer and a Daum ergo bike (serial) | Hardware list |
| 0.8 | Logo and visual identity ideas (sketches welcome, see Phase 3) | `design/sketches/` |

### 0.2 Licence options

All imported Incyclist code is MIT. Its MIT notices must stay, whatever licence is chosen for the project. The project licence decides the terms for **your** code and the combined app.

| Licence | What it means | Fit for this project |
|---------|---------------|----------------------|
| **MIT** (recommended) | Anyone may use, change and redistribute, also in closed products, as long as the notice is kept | Same licence as the imported code → one licence for the whole repo; simplest; compatible with app stores (relevant if mobile comes later) |
| Apache 2.0 | Like MIT, plus an explicit patent grant and a requirement to mark changes | Good alternative; slightly more paperwork (NOTICE file) |
| GPLv3 | Copyleft: anyone distributing a changed version must publish their source under GPLv3 | Prevents closed-source copies; but conflicts with Apple App Store terms (problem for a later iOS/Apple TV version) |

**Decision: MIT** (same licence as the imported Incyclist code).

**Exit criteria:** licence chosen, `desktop` licence confirmed, accounts and hardware ready.

---

## Phase 1 – One-time import and build environment

**Goal:** the imported code builds and runs from this repository, with no remaining connection to the Incyclist repositories.

| # | Task | Notes |
|---|------|-------|
| 1.1 | Initial commit: README, `LICENSE`, `.gitignore`, `PLAN.md`, root `package.json` (npm workspaces) | First commit on `main` |
| 1.2 | Choose a fixed snapshot of each source repository (latest release tag or commit) and record the commit SHAs in `docs/origin.md` | Traceability of the starting point |
| 1.3 | Import each repository into its folder **once** (`git subtree add` with history for attribution, or a plain copy) – `web-ui`, `desktop`, `services`, `devices`, `EventLogger` (not `ant-plus`). Keep each original `LICENSE` file in its folder; do not import test data/samples of unclear origin. Remove the temporary remotes afterwards | No upstream remotes remain |
| 1.4 | Rename the packages (table above) and replace every import of `incyclist-services`, `incyclist-devices`, `gd-eventlog` with the workspace packages | No `incyclist-*` npm dependencies remain |
| 1.5 | Wire the workspace: `web-ui` → `services`/`devices`/`addons`; `desktop` → `devices`, built `web-ui` (instead of `../build` / remote `pageUrl`) | Single `npm install` at the root |
| 1.6 | Pin all third-party dependency versions (lockfile committed) | Reproducible builds |
| 1.7 | Toolchain per OS: Node ≥ 24, Electron (version from `desktop/package.json`), Python + C/C++ build tools for native modules (`serialport`, `@stoprocent/noble`), FFmpeg | Setup guide in `docs/` |
| 1.8 | Build and run: `web-ui` dev server + Storybook, `desktop` app, connect to a real trainer | Smoke test |
| 1.9 | Run all imported test suites (jest, vitest, Playwright) and record the baseline | Baseline report |
| 1.10a | **Remove ANT+**: delete `packages/devices/src/antv2` and ANT+ references in device selection/pairing, the desktop `ant` feature and binding, the `usb` and `incyclist-ant-plus` dependencies, ANT+ options in settings and UI (incl. the ANT+ status badge) | No ANT+ code or `usb` dependency remains |
| 1.10b | CI licence check for npm dependencies (allow/deny lists in `docs/licensing/LICENSING.md` §6) | Fails on GPL/AGPL/SSPL/BUSL/unknown |
| 1.10 | CI (GitHub Actions): lint, tests, unsigned builds for Windows, macOS (x64 + arm64), Linux (x64 + arm64) on every pull request | Build artefacts |

**Exit criteria:** the app builds and runs on all three OSes from this repository alone; `npm ls` shows no `incyclist-*` or `gd-eventlog` packages; CI is green.

---

## Phase 2 – Rebrand and remove every tie to Incyclist

**Goal:** an app that never contacts Incyclist, never receives Incyclist updates, and cannot clash with an installed Incyclist.

### 2.1 Rebranding

| Item | Value | Where |
|------|-------|-------|
| App name / `productName` | `LH Cycle Training` | `apps/desktop/package.json`, `forge.config.js`, `config/*.json` |
| Package name | `lh-cycle-training` | `apps/desktop/package.json`, `apps/web-ui/package.json` |
| `desktopName` (Linux) | `lh-cycle-training.desktop` | `apps/desktop/package.json` |
| App ID / bundle ID | `io.github.shenphenchoedron-hue.lhcycletraining` | `forge.config.js`, electron-builder configs, entitlements |
| `repository` / `homepage` | `https://github.com/shenphenchoedron-hue/LH-Cycle-Training` | all `package.json` files |
| Author / copyright | Own name + "Based on Incyclist by Guido Doumen (MIT)" | `package.json`, About dialog, `THIRD_PARTY_NOTICES.md` |
| Icons (`.ico`, `.icns`, PNG set) | Generated from the final logo | `apps/desktop/res/icons` |
| macOS Bluetooth usage text | "LH Cycle Training uses Bluetooth to connect to your trainer and sensors." | `apps/desktop/entitlements` / Info.plist |
| **User data directory** | `LH Cycle Training` (separate from Incyclist's) | Electron `app.setPath('userData', …)` |
| Custom URL schemes (`incyclist:`) | `lhct:` | `apps/desktop/src/features/IncyclistScheme.js`, `packages/services` |
| Internal names (`Incyclist*` classes, log tags, user agent, file names) | Renamed where user-visible; internal identifiers renamed gradually | All packages |

### 2.2 Remove Incyclist infrastructure

| Incyclist endpoint | Used for | Action |
|--------------------|----------|--------|
| `updates.incyclist.com` – UI bundle updates | Remote replacement of the web UI (`apps/desktop/src/modules/AutoUpdate`) | **Remove the feature.** The UI is always the one shipped inside the app |
| `updates.incyclist.com` – app updates | Installer update checks | **Replace** with `electron-updater` reading **only** this project's GitHub Releases |
| `updates.incyclist.com/api/v1/maps` | Google Maps key proxy | Remove; OpenStreetMap map view only (Street View optional later with a user-supplied key) |
| `dlws.incyclist.com/api/v1/routes` | Route + video catalogue | Default = none; optional own local video server (Phase 4) |
| `dlws.incyclist.com` – active rides | "Ride with others" | Remove (possible own backend later, Phase 11) |
| `dlws.incyclist.com` – FIT convert | FIT conversion | Replace with local conversion |
| `videos.incyclist.com` | Hosted videos | Remove (local library) |
| `auth.incyclist.com` | Strava OAuth + token refresh | Replace: local OAuth flow with user-supplied Strava API keys, or own small open-source auth service |
| Remote logging (`RestLogAdapter`, `logRest`) | Sends logs to Incyclist | Remove; logs stay local, user can export them for bug reports |
| `Referer: https://incyclist.com` headers | Request identification (Overpass, fetch) | Replace with own identifier |
| Links to incyclist.com (privacy, help, manual) | UI links | Replace with own GitHub Pages docs |
| `updates.incyclist.com/ffmpeg/download/…` | Runtime download of an FFmpeg binary of unknown build/licence | Replace with an own **LGPL** FFmpeg build shipped with the app or downloaded from this project's releases; H.264 encoding via OS hardware encoders or OpenH264 (no GPL `libx264`) – see `docs/licensing/LICENSING.md` §4 |
| Map tile layers (OSM, Stadia, Esri, CARTO) | Map backgrounds | Keep OSM with attribution + identifying user agent, configurable tile URL; remove Stadia/Esri/CARTO by default (account/licence required) – see `LICENSING.md` §7 |

Tasks:
- 2.2.1 Search the whole repository for `incyclist.com`, `incyclist` and `Incyclist` and list every hit in `docs/backend-inventory.md` with the chosen action.
- 2.2.2 Change hard-coded URLs and defaults directly in `packages/services` and `apps/desktop` (we own the code now).
- 2.2.3 Remove dead code paths of removed features (UI bundle update, remote logging, active rides).
- 2.2.4 Privacy statement for LH Cycle Training (no telemetry).

**Exit criteria:** with network monitoring active, the app makes no requests to `*.incyclist.com`; the app only updates from this project's releases; it runs side by side with an installed Incyclist without sharing data.

---

## Phase 3 – UI redesign (guided by your SVG sketches)

**Goal:** a modern, consistent, significantly better-looking interface that matches your wishes.

### 3.1 Your SVG files as visual guidelines

The SVG files are **inspiration and direction, not a strict specification**. They show the look and feel you want (mood, colours, layout ideas, what matters on each screen). The implementation follows their spirit and fills in the rest: exact sizes, states, responsiveness, details that are not drawn.

Put them in a simple folder – no fixed structure or naming rules:

```
design/
└── sketches/     any SVG files: whole screens, parts of screens, colour ideas, logo ideas …
                  (optionally a short note next to a file: "I like the big power number here")
```

How they are used:

| What I take from the sketches | How |
|-------------------------------|-----|
| Overall style (dark/light, flat/glassy, rounded/sharp, calm/sporty) | Translated into the design principles and theme |
| Colour palette and fonts | Used as the starting point for the theme (fonts replaced by open-licensed equivalents if needed) |
| Layout ideas per screen (what is big, what is where, what is hidden) | Used as the basis for the page layouts, adapted to laptop, monitor and TV sizes |
| Logo / icon ideas | Used for the app icon and icon style |
| Anything not shown | Designed in the same style |

Each sketch is opened and rendered to an image to see it as you drew it. Where a sketch is ambiguous or technically hard to realise (e.g. too much information on the ride screen), the result follows the intention and the difference is explained.

Helpful but not required: text left as text, a quick note on what you like about a sketch, and sketches of the ride screen first (the most important screen).

### 3.1.1 Style direction (derived from the first sketches, 2026-10-08)

Sketches received (currently in the project root, to be moved to `design/sketches/`):
`incyclist_original_design_exact.svg` (paired devices), `incyclist_routes_exact_no_dock.svg` (routes),
`incyclist_search_exact.svg` (search), `incyclist_modal_boxes.svg` (dialog colour set, vector).

**Overall feel:** bright, colourful, sunny and inviting – a "holiday ride" mood instead of a dark sports cockpit. Large photographic scenery backgrounds, frosted-glass panels on top, saturated gradient cards, big bold headings, plenty of rounding.

| Element | Direction |
|---------|-----------|
| Backgrounds | Full-screen landscape/road photo per page, softly blurred behind content (stronger blur behind dense content such as lists) |
| Panels | Frosted glass: white at ~70–85 % opacity with backdrop blur, thin white border, large radius, soft shadow |
| Navigation | Vertical sidebar on the left (User, Settings, Devices, Search, Routes, Workouts, Activities, Exit); icon above label; active item as a purple gradient tile |
| Page titles | Centred, uppercase, heavy weight, dark navy, with a short purple underline accent |
| Section titles | Large, bold, sentence case, dark navy ("My Routes", "Selected For Me") |
| Route cards | Photo on top; bottom half a saturated gradient with white title, country flag, elevation silhouette, distance and climb icons |
| Route list rows | Full-width gradient bars: thumbnail, elevation profile, title, flag, type icons (video, loop / point-to-point), distance, elevation, chevron |
| Device tiles | Pastel gradient tiles with soft wave shapes, large colourful illustrated icon, coloured uppercase label, white pill "Search ›" button; one colour per device type |
| Dialogs | Solid gradient (top-left light → bottom-right dark), header with icon chip + title + subtitle + close, divider lines in white ~22 %, light input fields, translucent selects, ghost "Cancel" + light filled primary button |
| Primary actions | Orange gradient pill (e.g. "Skip", "OK"); purple gradient for import/main CTA |
| Status badges | Small solid colour tiles with icon + label (BLE, Serial, TCP/Direct Connect – no ANT+) |

**Colour palette (from `incyclist_modal_boxes.svg`):**

| Name | Gradient (top → bottom) | Light tint (input fields) |
|------|-------------------------|---------------------------|
| Purple (brand / primary) | `#7628EA` → `#390C99` | `#EEE8FF` |
| Blue | `#087BE9` → `#003EAE` | `#E5EFFF` |
| Magenta | `#CF19A8` → `#830C7A` | `#FCE5F6` |
| Teal | `#009FC2` → `#006582` | `#DEF6FA` |
| Orange (action) | `#FF831B` → `#D84A00` | `#FFF0E2` |
| Green (success) | `#38A76C` → `#126443` | `#E5F5EB` |
| Text dark | Navy, approx. `#14123A` (page/section titles) | – |
| White overlays | 8 %, 14–16 % (fields, chips), 22 % (dividers), 45–50 % (borders), 78 % (subtitles) | – |

Gradient direction: `x1=0 y1=0 → x2=0.15 y2=1` (mostly vertical, slightly diagonal) + a subtle white sheen.

**Typography:** Inter (open source, SIL OFL). Weights 400 / 600 / 700 (headings up to 800). Dialog scale from the sketch: title 21, labels 14 semibold, input text 15–16, subtitle 13. Page titles and card titles scale up for TV distance.

**Shape:** radii 22 (dialogs, large cards), 13 (icon chips), 9–10 (inputs, buttons), pills for search/action buttons.

**Points to resolve during implementation:**
- Background images: the sketch backgrounds are **generated with ChatGPT** and can be used. Under OpenAI's terms the output belongs to the user. Purely AI-generated images are generally not protected by copyright (e.g. US Copyright Office guidance), so they are effectively free for anyone to reuse, which suits an open-source project. Rules for using them:
  - Store final images in `apps/web-ui/src/assets/backgrounds/` with a `README` stating they are AI-generated (ChatGPT/OpenAI), the date, and that they are released as **CC0**. Do not claim them as human-made (OpenAI's terms forbid that).
  - Keep the embedded C2PA "content credentials" metadata in the files (already present in the sketch PNGs).
  - Check every image for recognisable logos, brand names, real people or copies of known photos (jerseys, bikes, signs); regenerate if found.
  - Generate final backgrounds at ≥ 2560 px width (the sketch images are 1672 px) so they stay sharp on large and 4K screens; export as WebP/AVIF for size.
  - Any other images (illustrations, icons) must be own, AI-generated under the same rules, or open-licensed.
- Card colours: decide whether colours cycle decoratively (as in the sketches) or carry meaning (e.g. route type, difficulty or source).
- Readability on photo backgrounds: keep blur + glass panels behind all text; check contrast of white text on the lighter gradient ends (orange, teal).
- Flags: open flag icon set (e.g. `flag-icons`, MIT).
- Screens not yet sketched: **ride screen (HUD)**, workouts, activities/analysis dashboard, settings, import/pairing wizard – they will be designed in the same style; sketches for the ride screen are the most valuable next input.
- App name in the window title bar becomes "LH Cycle Training".

### 3.2 Implementation in `apps/web-ui`

| # | Task |
|---|------|
| 3.3.1 | UI audit: screenshot every page and dialog of the imported app as a reference for what must be redesigned |
| 3.3.2 | Design principles: readable at a distance while riding, dark-first, large touch/click targets, TV-friendly |
| 3.3.3 | Theme (colours, fonts, spacing, radii, shadows) derived from the sketches |
| 3.3.4 | Replace hard-coded `vw`/`vh` sizes with a responsive scale (CSS custom properties / `clamp()`) |
| 3.3.5 | Single, consistent icon set in the style of the sketches (based on an open icon set such as Lucide / Phosphor) |
| 3.3.6 | Rebuild **atoms** (buttons, inputs, toggles, sliders, cards, badges, metric tiles) |
| 3.3.7 | Rebuild **molecules** (lists, dialogs, menus, device tiles, charts) |
| 3.3.8 | Rebuild **modules** and **pages** in order: ride HUD → route library → pairing → workouts → activities/analysis → settings → import wizard → remaining dialogs |
| 3.3.9 | Keep the wrapper/view split (wrapper talks to services, view is pure and lives in Storybook) |
| 3.3.10 | Accessibility: contrast, focus states, keyboard navigation, font scaling |
| 3.3.11 | i18n: all strings through the translation mechanism |
| 3.3.12 | Remove the old components once a page is migrated (no need to keep them – there is no upstream to merge) |
| 3.3.13 | Visual regression tests: Storybook + Playwright screenshots |

**Exit criteria:** every page has the new look in the spirit of the sketches and you are happy with it; no old components or `vw`/`vh` layouts remain; Storybook covers all components.

---

## Phase 4 – Local video library

**Goal:** users add videos from their own disk, a NAS, or a local server.

### 4.1 File import
- New UI for the existing import functions (single file, multiple files, folder scan, drag & drop) – logic in `packages/services` (`RouteLibraryScannerService`, `RouteListService.import`).
- Supported formats (already implemented): GPX, Incyclist XML, Ergoplanet EPM, Tacx PGMF/RLV, KWT, Bikelab, multi-XML; MP4 plays directly, AVI is converted via FFmpeg.
- "Video library folders" setting: remembered folders (local or mounted NAS shares), rescan with one click.

### 4.2 Local video server (`tools/video-server`)
| # | Task |
|---|------|
| 4.2.1 | Node.js service that scans folders containing video routes (XML/EPM/PGMF/RLV/KWT + video files) |
| 4.2.2 | Reuse the parsers from `packages/services` |
| 4.2.3 | Route API: `GET {base}?private=false`, `GET {base}/?type=video`, `GET {base}/{id}`, `GET {base}/{id}/preview`, optional `GET {base}/country` |
| 4.2.4 | Video delivery with HTTP range requests, correct MIME types, CORS headers |
| 4.2.5 | Stream mode (`videoUrl` = http URL) and download mode (`requiresDownload` + `downloadUrl`) |
| 4.2.6 | Preview images via FFmpeg |
| 4.2.7 | Optional mDNS/Bonjour discovery |
| 4.2.8 | Packaging: standalone binary + Docker image (NAS friendly) |

### 4.3 App integration
- Settings: "Video server" address (sets `ROUTE_API`), "Find server" (mDNS), connection test.
- Route library shows whether a video is local, on the server, or downloaded.

**Exit criteria:** videos added by file picker, NAS folder, and local server can each be ridden with correct speed/gradient behaviour.

---

## Phase 5 – Video pairing wizard (video + GPX/FIT)

**Goal:** "Add video" → "Add GPX/FIT" → ride. No manual XML.

Implementation: logic in `packages/addons`, FFmpeg/ffprobe bindings in `apps/desktop`, wizard UI in `apps/web-ui`.

| Step | Function |
|------|----------|
| 5.1 | **Select video** – duration, frame rate, resolution, codec, creation time (ffprobe) |
| 5.2 | **Select track** – GPX or FIT (Garmin/Wahoo) |
| 5.3 | **Automatic alignment** – video creation time vs. track timestamps (handle UTC/local-time offsets) |
| 5.4 | **Visual sync** – video + map side by side, scrubber, "this frame = this point"; moving map marker as confirmation |
| 5.5 | **Trim** – start/end |
| 5.6 | **Clean-up** – detect and optionally cut stops; elevation smoothing |
| 5.7 | **Convert (optional)** – H.265/4K → H.264 1080p "faststart" via FFmpeg, with progress and cancel |
| 5.8 | **Metadata** – title, country (auto), description, preview image |
| 5.9 | **Save** – route XML + GPX next to the video (same open XML format the import already reads, so files stay portable and can be served by the video server), then import automatically |

**Exit criteria:** phone video + Garmin FIT and camera video + GPX each become rideable routes without editing any file by hand.

---

## Phase 6 – GoPro import (embedded GPS)

**Goal:** "Add GoPro video" → ride. The GPS track is extracted from the video itself.

| # | Task |
|---|------|
| 6.1 | Detect GoPro files and the GPMF telemetry track |
| 6.2 | Extract GPS (GPS5 for HERO5–HERO10, GPS9 for HERO11+), UTC time (GPSU), fix/precision (GPSF/GPSP/DOP) with `gpmf-extract` + `gopro-telemetry` (MIT) or GoPro's `gpmf-parser` (Apache 2.0) |
| 6.3 | Join chaptered recordings (`GH01xxxx`, `GH02xxxx`, …): lossless concat with FFmpeg + merged telemetry |
| 6.4 | Discard samples without fix / with poor precision |
| 6.5 | Elevation correction: smoothing, optional open elevation data (Copernicus DEM / SRTM) |
| 6.6 | Continue in the pairing wizard from step 5.5 (sync automatic, visual check available) |
| 6.7 | Later: DJI Osmo Action, Insta360, Garmin VIRB |

**Exit criteria:** single-file and chaptered GoPro recordings (HERO8+ and HERO11+) become rideable routes with plausible gradients.

---

## Phase 7 – Training history in SQLite + analysis

**Goal:** local, private, long-term training analysis. Because the code is fully owned, SQLite becomes the **primary** activity store.

### 7.1 Storage
| # | Task |
|---|------|
| 7.1.1 | Add `better-sqlite3` (MIT) to `apps/desktop`; expose a narrow data-access API to the UI through the bindings layer |
| 7.1.2 | Schema with versioned migrations: `activities`, `samples` (per second, optionally a compressed blob per activity), `laps`, `athlete_history` (FTP, weight, HR max/rest with effective dates), `metrics_cache` (power curve, zones, load per activity) |
| 7.1.3 | Replace the JSON activity repository in `packages/services` (`activities/base/repo`) with the SQLite repository |
| 7.1.4 | One-time migration of existing JSON activities (e.g. from a previous installation) into SQLite |
| 7.1.5 | Importers: FIT files (Garmin/Wahoo), Strava bulk export (zip with FIT/GPX/TCX) |
| 7.1.6 | Backup/restore (single SQLite file), export (FIT/TCX per activity, CSV) |

### 7.2 Analysis engine (`packages/addons`)
- Per activity: weighted average power, intensity, training load, variability, time in power/HR zones, aerobic decoupling, best efforts
- Over time: fitness / fatigue / form (42-day / 7-day load model), power-duration curve (all time / 90 days / season), estimated FTP, weekly/monthly volume, records, per-route best times
- Generic metric names (TSS/NP/IF are TrainingPeaks trademarks); GoldenCheetah (GPL) only as a formula reference, no code copied

### 7.3 UI (`apps/web-ui`)
- Dashboard: form/fitness chart, weekly volume, recent activities, records
- Activity detail: power/HR/cadence/elevation charts, map, zones, laps, best efforts
- Power curve, calendar heatmap, athlete settings history
- Charts: uPlot (MIT) for large time series, ECharts (Apache 2.0) for dashboards

**Exit criteria:** every new ride appears in the analysis immediately; imported FIT files are included; dashboards load fast with several years of data.

---

## Phase 8 – Quality assurance

| # | Task |
|---|------|
| 8.1 | OS matrix: Windows 10/11, macOS (Intel + Apple Silicon), Linux (Ubuntu LTS x64 + ARM64) |
| 8.2 | Hardware matrix (0.7): pairing, ERG mode, simulation mode, virtual shifting, reconnects |
| 8.3 | Video matrix: MP4 H.264, H.265, AVI, 4K, chaptered GoPro, videos > 2 h, NAS and server playback |
| 8.4 | Automated tests: unit (`packages/*`), Storybook visual tests, Playwright end-to-end, device simulator from `packages/devices` |
| 8.5 | Beta group of riders; issue templates with log export |

---

## Phase 9 – Packaging, signing and distribution

| # | Task |
|---|------|
| 9.1 | Windows: signed installer; winget manifest |
| 9.2 | macOS: signed + notarised DMG (x64 + arm64 or universal); Homebrew cask |
| 9.3 | Linux: AppImage (x64 + arm64), Flatpak (Flathub), optional deb/rpm |
| 9.4 | Release CI: tag → build → sign → GitHub Release → update feed for `electron-updater` (this project only) |
| 9.5 | Licence notices complete in `THIRD_PARTY_NOTICES.md` and the About dialog; run the release checklist in `docs/licensing/LICENSING.md` §10 |
| 9.6 | `tools/video-server` as binaries + Docker image (`ghcr.io/shenphenchoedron-hue/lh-cycle-training-video-server`) |
| 9.7 | Releases at `https://github.com/shenphenchoedron-hue/LH-Cycle-Training/releases`; tags `v1.0.0` (app), `video-server-v1.0.0` (server) |

---

## Phase 10 – Documentation and community

| # | Task |
|---|------|
| 10.1 | User docs: installation, Linux setup (BlueZ, serial permissions), adding videos, pairing wizard, GoPro import, video server, analysis |
| 10.2 | Developer docs: architecture, workspace setup, package overview, theme and component guidelines |
| 10.3 | `README`, `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, `SECURITY.md`, issue/PR templates |
| 10.4 | Website via GitHub Pages (`https://shenphenchoedron-hue.github.io/LH-Cycle-Training`), built from `docs/` |
| 10.5 | Credit Incyclist and its authors (README, About dialog) |

---

## Phase 11 – Later (out of scope for the first release)

- Mobile (Android/iOS)
- Apple TV / Android TV (needs a different technology – see earlier analysis)
- Street View / Satellite view with a user-supplied Google Maps key
- Ride with others with an own backend
- More camera formats (DJI, Insta360, VIRB)

---

## Milestones

| Milestone | Content | Phases |
|-----------|---------|--------|
| **M1 – Independent app** | Imported, renamed, builds on all OSes, no Incyclist code dependencies, servers or updates | 0, 1, 2 |
| **M2 – New look** | New design on all pages, guided by the sketches | 3 |
| **M3 – Own video library** | File/NAS import + local video server | 4 |
| **M4 – Pairing wizard** | Video + GPX/FIT, then GoPro | 5, 6 |
| **M5 – Analysis** | SQLite history + dashboards | 7 |
| **M6 – Public release 1.0** | QA, signed installers, docs, website | 8, 9, 10 |

Sketches for M3–M5 screens can be made any time; they are not a precondition. Phases 4–7 are largely independent once M1 is done.

---

## Risks and mitigations

| Risk | Impact | Mitigation |
|------|--------|------------|
| Device protocols and trainer quirks are now maintained only by this project | Bugs with specific trainers are not fixed by anyone else | Hardware test matrix, device simulator tests, good logging/log export for user bug reports |
| Large imported codebase to understand | Slow start | Architecture notes in `docs/` while importing; keep `services`/`devices` structure initially |
| `desktop` repository has no LICENSE file | Legal uncertainty | Written confirmation from the author (0.3); fallback: rebuild the small Electron shell around the clearly MIT-licensed packages |
| Strava API terms (secret keys) | Integration blocked in an open-source repo | User-supplied API keys or own auth service |
| BLE behaviour differs per OS (especially Linux/BlueZ) | Pairing problems | Hardware matrix (8.2) |
| H.265 / 4K playback limits in Chromium | Videos do not play | Conversion step in the wizard (5.7) |
| GoPro GPS noise / missing fix | Wrong gradients | Fix filtering, smoothing, DEM correction (6.4, 6.5) |
| Sketch intention misread | Result differs from your wish | Show new screens early in Storybook for feedback; adjust in short rounds |
| Trademarks (metric names, remaining "Incyclist" references) | Forced renaming | Full rename (2.1, 2.2.1), generic metric names (7.2) |

---

## Decision log

| # | Decision | Status |
|---|----------|--------|
| D1 | Fork Incyclist instead of rewriting | Decided |
| D2 | Desktop first (Windows, macOS, Linux); mobile/TV later | Decided |
| D3 | Fully open source | Decided |
| D4 | No Street View in the first release | Proposed |
| D5 | Licence: MIT | Decided |
| D6 | Product name: LH Cycle Training | Decided |
| D7 | **No ANT+** – removed from the app (connection problems with trainers) | Decided |
| D8 | Repository: `github.com/shenphenchoedron-hue/LH-Cycle-Training` | Decided |
| D9 | **Independent fork:** one-time import of all Incyclist code into one monorepo; no upstream sync, no Incyclist npm packages, servers or updates | Decided |
| D10 | Website and update feed on GitHub Pages / GitHub Releases | Proposed |
| D11 | SVG sketches are visual guidelines for the redesign, not a strict specification | Decided |
| D12 | SQLite is the primary activity store | Proposed |

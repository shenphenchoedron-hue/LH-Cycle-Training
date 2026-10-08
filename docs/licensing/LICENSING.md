# Licence Compliance Guide – LH Cycle Training

This document lists every licence obligation of the project, what has to be done about it, and
its current status. It is the reference for contributors and the checklist for each release.

Last review: 2026-10-08 (before code import).

---

## 1. Project licence

| Item | Status |
|------|--------|
| Licence for LH Cycle Training's own code | **MIT** – decided 2026-10-08 |
| `LICENSE` file in the repository root | ✔ "Copyright (c) 2026 Lone Hansen and LH Cycle Training contributors" |

MIT is the same licence as the imported Incyclist code, so the whole repository is under one
permissive licence. Contributions are accepted under the same MIT licence (inbound = outbound).
Consequence of MIT: GPL components (e.g. a GPL FFmpeg build with `libx264`) must not be used – see section 4.

---

## 2. Imported code (one-time import from Incyclist)

| Component | Folder | Licence | Obligation | Status |
|-----------|--------|---------|------------|--------|
| incyclist/web-ui | `apps/web-ui` | MIT, © 2021 Incyclist | Keep copyright + licence notice | Text in `THIRD_PARTY_NOTICES.md` ✔ |
| incyclist/services | `packages/services` | MIT, © 2023 Incyclist | Keep copyright + licence notice | Text in `THIRD_PARTY_NOTICES.md` ✔ |
| incyclist/devices | `packages/devices` | MIT, © 2021 Incyclist | Keep copyright + licence notice | Text in `THIRD_PARTY_NOTICES.md` ✔ |
| incyclist/desktop | `apps/desktop` | MIT declared in `package.json`, **no LICENSE file** | Get written confirmation | Request drafted: `author-license-request.md` |
| gd-eventlog | `packages/eventlog` | MIT declared in `package.json`, **no LICENSE file** | Get written confirmation, or replace | Request drafted (same message) |
| incyclist/ant-plus | – | MIT, © 2015 Alessandro Vergani | – | **Not imported** (no ANT+ in the app) |

Rules:
1. Keep the original `LICENSE` file inside each imported folder (`apps/web-ui/LICENSE`, …). Do not delete or edit it.
2. Keep existing copyright headers in source files.
3. Record the imported commit SHAs in `docs/origin.md` and in `THIRD_PARTY_NOTICES.md`.
4. Ship `THIRD_PARTY_NOTICES.md` with every installer, and show it in the About dialog.
5. Credit: "Based on Incyclist by Guido Doumen" in the README and the About dialog.
6. **Do not import** files that are not covered by the MIT licence, or whose origin is unclear:
   - test data, sample GPX/videos and preview images (`testdata/`, `samples/`, `__tests__/testdata`)
   - demo routes pointing to `videos.incyclist.com`

   Use them only for local tests, never in the app bundle.

---

## 3. Names and trademarks

The MIT licence covers code only, not names or logos.

| Name | Rule |
|------|------|
| Incyclist (name, logo, icons) | Remove from the app, installers, window titles, URL schemes and user agents. Allowed only as a credit ("based on Incyclist") |
| Zwift (Play/Click), Wahoo, Tacx, Daum, Kettler, Garmin, Strava, Komoot, intervals.icu | Only descriptive use ("works with Wahoo KICKR"). No logos, unless the brand guidelines allow it (see Strava, section 7) |
| TSS, NP, IF (TrainingPeaks) | Use generic metric names instead ("Training Load", "Weighted Power", "Intensity") |

---

## 4. FFmpeg (video conversion)

**Finding:** the imported desktop app does not ship FFmpeg. It downloads a binary at runtime from
`https://updates.incyclist.com/ffmpeg/download/<platform>/<arch>` (`apps/desktop/src/features/VideoScheme/ffmpeg.js`).
The build configuration, and therefore the licence (LGPL or GPL), of that binary is unknown.
The download also depends on an Incyclist server.

**Action:**
1. Remove the download from Incyclist's server.
2. Ship our own FFmpeg build with the app, or download it from this project's GitHub Releases. It runs as a **separate executable** (no linking).
3. Use an **LGPL** build: configured without `--enable-gpl` and without `--enable-nonfree`.
   - Windows/Linux: e.g. the "lgpl" variants of BtbN/FFmpeg-Builds.
   - macOS: our own build from source in CI.
4. **H.264 encoding** (pairing wizard conversion): `libx264` is GPL and cannot be used in an LGPL build. Use one of:
   - the OS hardware encoders: `h264_videotoolbox` (macOS), `h264_mf` (Windows), `h264_vaapi`/`h264_nvenc`/`h264_qsv` (Linux)
   - `libopenh264` (BSD)
   - AV1 via `libsvtav1` (BSD)

   Decoding uses FFmpeg's built-in decoders.
5. With every release:
   - include the LGPL 2.1 text
   - include the FFmpeg version and the full `configure` line
   - attach the exact FFmpeg source archive to the GitHub Release (or provide a written offer)
   - add the notice to `THIRD_PARTY_NOTICES.md`
6. Patents: H.264/HEVC are patent-encumbered. OS encoders shift patent licensing to the OS vendor. Cisco's OpenH264 binaries are royalty-covered only when downloaded from Cisco.

A GPL FFmpeg build is **not** used, because the project is MIT-licensed.

---

## 5. Electron / Chromium

- Electron (MIT) and Chromium ship their own licence collections: `LICENSE`, `LICENSES.chromium.html`.
- Keep both files in the packaged app; do not strip them during packaging.
- Chromium's built-in media codecs are covered by Electron's distribution; no extra action.

---

## 6. npm dependencies

Rules:
- **Allowed** without review: MIT, ISC, BSD-2-Clause, BSD-3-Clause, Apache-2.0, 0BSD, CC0-1.0, Unlicense, BlueOak-1.0.0, Python-2.0, CC-BY-4.0 (data/assets).
- **Review needed:** MPL-2.0 (file-level copyleft, OK if unmodified), LGPL (only as a separately replaceable library or executable).
- **Not allowed:** GPL/AGPL, SSPL, BUSL, Commons Clause, "no licence" / unknown, proprietary.

Tasks:
1. CI licence check on every pull request (e.g. `license-checker-rseidelsohn --production --failOn "<denied list>"`).
2. Release step: generate the dependency licence list (`npm run licenses`) and append it to `THIRD_PARTY_NOTICES.md` section 6.
3. Known native modules after removing ANT+: `@stoprocent/noble` (BLE, MIT) and `serialport` (MIT). Removing ANT+ also removes `usb`/libusb (LGPL-2.1) and `incyclist-ant-plus`.
4. Replace `react-vis` (unmaintained) with uPlot (MIT) / ECharts (Apache-2.0) during the redesign.

---

## 7. Online services and their terms

| Service | Used for | Obligations |
|---------|----------|-------------|
| OpenStreetMap data | Maps, free ride | Show "© OpenStreetMap contributors" on every map (ODbL) |
| OSM standard tiles (`tile.openstreetmap.org`) | Map view | OSMF Tile Usage Policy: <ul><li>attribution</li><li>a user agent that identifies LH Cycle Training</li><li>no heavy or bulk use, no prefetching</li><li>may be blocked at scale</li></ul> Make the tile URL configurable |
| Stadia Maps, Esri (topo/satellite), CARTO tiles | Alternative map layers in the imported code | Each needs an account/API key and its own terms (Esri: ArcGIS licence; Stadia/CARTO: free tiers with restrictions). **Remove by default**; offer only with a user-supplied key, or after the terms are checked |
| Overpass API | Free ride road network | Fair-use policy (moderate request rate), OSM attribution, identifying user agent |
| Geocoding (location search) | Search for a start location | If Nominatim is used: max 1 request/second, identifying user agent, attribution, no bulk use |
| Strava API | Upload activities | Accept the Strava API Agreement. Follow the Strava brand guidelines ("Connect with Strava" button, "Powered by Strava"). Do not use "Strava" in the app name. The client secret is never stored in the repository. Respect the data-use restrictions |
| Komoot, intervals.icu | Route import, workout sync | Accept their API terms; credentials supplied by the user or through own registration, never stored in the repository |
| Google Maps / Street View | – | Removed (needed a key held by Incyclist) |
| Incyclist servers (`*.incyclist.com`) | – | All removed (see `PLAN.md` Phase 2) |

---

## 8. Fonts, icons, images

| Asset | Licence | Obligation |
|-------|---------|------------|
| Inter font | SIL OFL 1.1 | Bundle `OFL.txt` next to the font files; do not sell the font on its own; do not use the reserved font name for modified versions |
| Icon set (Lucide ISC / Phosphor MIT – to be chosen) | Permissive | Licence text in `THIRD_PARTY_NOTICES.md` |
| Flag icons (`flag-icons`, MIT) | MIT | Licence text in `THIRD_PARTY_NOTICES.md` |
| AI-generated images (ChatGPT/OpenAI) | Released as CC0 1.0 by the project | <ul><li>List them in `apps/web-ui/src/assets/ASSETS.md` (tool, date, "AI-generated")</li><li>Never present them as human-made</li><li>Keep their C2PA metadata</li><li>Check for logos, brands, real people and copied photos</li></ul> |
| Design sketches (`design/sketches/`) | Reference only, not shipped | See `design/sketches/README.md` |

---

## 9. Removed features (no licence obligations)

| Feature | Reason removed | Licence effect |
|---------|----------------|----------------|
| ANT+ | Causes connection problems with trainers (project decision) | No ANT+ adopter terms or ANT+ trademarks; no `usb`/libusb (LGPL); no `incyclist-ant-plus` |
| Google Street View / Satellite | Needed Incyclist's API key | No Google Maps Platform terms |
| Remote UI updates, remote logging, active rides | Tied to Incyclist servers | – |

---

## 10. Release checklist

- [x] `LICENSE` present (MIT)
- [ ] Original `LICENSE` files present in `apps/web-ui`, `apps/desktop`, `packages/services`, `packages/devices`, `packages/eventlog`
- [ ] Author confirmation for `desktop` and `gd-eventlog` stored in `docs/licensing/` (or the fallback carried out)
- [ ] `THIRD_PARTY_NOTICES.md` complete: no **PENDING** items left; dependency list regenerated
- [ ] CI licence check green (no denied licences)
- [ ] FFmpeg: LGPL build, configure line, licence text and source archive attached to the release
- [ ] Electron `LICENSE` + `LICENSES.chromium.html` present in every installer
- [ ] About dialog shows the credits and third-party notices
- [ ] No "Incyclist" name or logo in user-visible places (except the credit)
- [ ] No requests to `*.incyclist.com` (network check)
- [ ] Map attribution visible; tile and geocoding providers comply with their policies
- [ ] Strava button and logo follow the brand guidelines; no secrets in the repository
- [ ] `ASSETS.md` lists every AI-generated or third-party image with its licence
- [ ] Font licence files bundled

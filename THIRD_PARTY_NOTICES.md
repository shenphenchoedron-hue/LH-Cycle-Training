# Third-Party Notices

LH Cycle Training contains software and other material developed by third parties.
This file reproduces the copyright and licence notices required by their licences.
It is shipped with every release of the app and shown in the app's About dialog.

> Status: initial version created before the code import (2026-10-08). Sections marked
> **PENDING** are completed during Phase 1 (code import) and Phase 9 (release). The full list of
> npm dependency licences is generated automatically at release time (see `docs/licensing/LICENSING.md`).

---

## 1. Incyclist

LH Cycle Training is an independent fork of Incyclist (https://github.com/incyclist), created by
Guido Doumen. The following Incyclist components are included, in modified form.

### 1.1 Incyclist web-ui – `apps/web-ui`

Source: https://github.com/incyclist/web-ui (imported snapshot: **PENDING – commit SHA**)

```
MIT License

Copyright (c) 2021 Incyclist

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

### 1.2 Incyclist services – `packages/services`

Source: https://github.com/incyclist/services (imported snapshot: **PENDING – commit SHA**)

```
MIT License

Copyright (c) 2023 Incyclist

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

### 1.3 Incyclist devices – `packages/devices`

Source: https://github.com/incyclist/devices (imported snapshot: **PENDING – commit SHA**; ANT+ code removed)

```
MIT License

Copyright (c) 2021 Incyclist

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

### 1.4 Incyclist desktop – `apps/desktop`

Source: https://github.com/incyclist/desktop (imported snapshot: **PENDING – commit SHA**)

**PENDING:** the repository declares `"license": "MIT"` in `package.json` but has no LICENSE file.
The author's confirmation has been requested (`docs/licensing/author-license-request.md`).
Once confirmed, the MIT text with the confirmed copyright line is inserted here.

### 1.5 gd-eventlog – `packages/eventlog`

Source: https://github.com/gdoumen/EventLogger, npm `gd-eventlog` (imported version: **PENDING**)

**PENDING:** the package declares `"license": "MIT"` but contains no LICENSE file. Confirmation
requested together with 1.4. Fallback: replaced by an own implementation (then this section is removed).

---

## 2. Fonts

**PENDING (Phase 3):** Inter – SIL Open Font License 1.1 – https://github.com/rsms/inter.
The full OFL text is included as `apps/web-ui/src/assets/fonts/OFL.txt` and referenced here.

---

## 3. Images and icons

- AI-generated images (ChatGPT / OpenAI) are released by this project under CC0 1.0. They are listed in `apps/web-ui/src/assets/ASSETS.md`.
- **PENDING (Phase 3):** icon set (e.g. Lucide – ISC, or Phosphor – MIT) and flag set (e.g. `flag-icons` – MIT). The licence texts are added here.

---

## 4. Map data and tiles

- Map data © OpenStreetMap contributors, available under the Open Database License (ODbL) – https://www.openstreetmap.org/copyright. Attribution is shown on every map view.
- **PENDING (Phase 2):** the attribution for the chosen tile provider.

---

## 5. Bundled executables

**PENDING (Phase 1/9):** FFmpeg – LGPL 2.1 or later build. The notice, licence text, build configuration and a source code link or written offer are added here (see `docs/licensing/LICENSING.md`, section FFmpeg).

---

## 6. npm dependencies

**PENDING (Phase 9):** generated list of all production dependencies with their licence texts (`npm run licenses`), appended at release time.

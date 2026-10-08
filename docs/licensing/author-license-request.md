# Licence confirmation request to the Incyclist author

Two components that LH Cycle Training imports state "MIT" in their `package.json`, but their
repositories contain **no LICENSE file** (checked 2026-10-08):

| Component | Repository | `package.json` licence | LICENSE file |
|-----------|------------|------------------------|--------------|
| Incyclist desktop app | https://github.com/incyclist/desktop | MIT | missing |
| gd-eventlog (logging library) | https://github.com/gdoumen/EventLogger (npm: `gd-eventlog`) | MIT | missing (also missing in the npm package) |

The other imported repositories (`web-ui`, `services`, `devices`) have proper MIT LICENSE files.

## Status

- **Sent 2026-10-08** as GitHub issue https://github.com/incyclist/desktop/issues/222 (covers both `desktop` and `gd-eventlog`).
- Answer: **pending**. When it arrives, save it as `author-license-confirmation.md` in this folder and complete sections 1.4 and 1.5 in `THIRD_PARTY_NOTICES.md`.

## How to send it

Send it as a GitHub issue on `incyclist/desktop` (public and traceable), or by e-mail to info@incyclist.com.
Keep the answer: save the issue link or the e-mail in this folder as `author-license-confirmation.md`.

## Draft message

> **Subject:** Licence confirmation for incyclist/desktop and gd-eventlog
>
> Hello Guido,
>
> Thank you for making Incyclist open source. I am starting an independent, open-source
> indoor-cycling app called "LH Cycle Training", based on the Incyclist code
> (https://github.com/shenphenchoedron-hue/LH-Cycle-Training). Incyclist and you will be credited
> in the README and in the app's About dialog.
>
> The repositories `incyclist/web-ui`, `incyclist/services` and `incyclist/devices` have MIT
> LICENSE files. Two repositories only declare MIT in `package.json` and have no LICENSE file:
>
> - https://github.com/incyclist/desktop
> - https://github.com/gdoumen/EventLogger (npm package `gd-eventlog`)
>
> Could you confirm that both are licensed under the MIT License, and, if possible, add a
> LICENSE file to both repositories? Please also tell me which copyright line you would like to
> appear (for example "Copyright (c) 2020 Guido Doumen" or "Copyright (c) Incyclist").
>
> Thank you very much,
> Lone Hansen

## Fallback if there is no answer

- **gd-eventlog:** replace it with a small logging module of our own in `packages/eventlog`. It is a small library, so this is a minor task.
- **desktop:** rebuild the Electron shell. Use the MIT-licensed `web-ui`/`services`/`devices` code as the base, and only use `incyclist/desktop` as a functional reference, without copying its code.

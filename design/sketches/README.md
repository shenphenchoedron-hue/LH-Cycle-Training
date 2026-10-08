# Design sketches

These files are **visual guidelines** for the LH Cycle Training user interface (see `PLAN.md`, Phase 3).
They are design references only and are **not shipped** inside the app.

| File | Content | Origin |
|------|---------|--------|
| `incyclist_original_design_exact.svg` | Paired devices screen (SVG wrapping one embedded PNG) | Background, illustrations and layout generated with ChatGPT (OpenAI) |
| `incyclist_routes_exact_no_dock.svg` | Routes screen (SVG wrapping one embedded PNG) | Generated with ChatGPT (OpenAI) |
| `incyclist_search_exact.svg` | Search screen (SVG wrapping one embedded PNG) | Generated with ChatGPT (OpenAI) |
| `incyclist_modal_boxes.svg` | Dialog colour collection (pure vector) | Created for this project |

## AI-generated content

- The images embedded in the first three files were generated with ChatGPT (OpenAI) in October 2026.
- Under OpenAI's terms of use the output belongs to the user who generated it. Purely AI-generated images are generally not protected by copyright.
- They must not be presented as human-made.
- The embedded C2PA "content credentials" metadata is kept intact.

## Third-party elements visible in the sketches

The sketches show elements that are **not** owned by this project and are only used here as layout references:

- The name "Incyclist" in window titles and file names. It refers to the original app this project is forked from. The final app uses the name "LH Cycle Training".
- Route names, distances and thumbnails that resemble Incyclist's route catalogue.
- Country flags (the final app uses an open-licensed flag set, e.g. `flag-icons`, MIT).
- The macOS window chrome and menu bar.

## Rules for final assets derived from these sketches

Final backgrounds, illustrations and icons used in the app:

1. Are either created by the project, AI-generated, or carry a compatible open licence (CC0, CC BY, MIT, OFL, …).
2. AI-generated assets are listed in `apps/web-ui/src/assets/ASSETS.md` with tool, date and the statement that they are AI-generated, and are released under **CC0 1.0**.
3. Are checked for recognisable logos, brand names, real people and copies of known photographs before use; any image with such content is regenerated.
4. Keep their C2PA metadata where present.

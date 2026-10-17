# Mixweight — notes for Claude Code

Mixweight mixes the named styles of installed (or dropped) fonts letter by letter, and exports the result as a `.jsx` script that rebuilds it as **live, editable text** in Illustrator, InDesign and Photoshop. Live at https://mixweight.pages.dev (Cloudflare Pages). The tool is currently at v3.2; the next release is **V4**.

The owner is a graphic designer, and Mixweight is a visual tool for designers, not an engineering project. Judge every change by how the type looks and whether it survives export, not only by whether the code runs.

## Hard rules

1. **The `.jsx` export must stay editable.** Nothing in it gets outlined or rasterised. Each letter run is `[text, fontIndex, size, baselineShift, tracking, rotation, strokeWidth]` (see `buildData()`); boxes, guidelines and Photoshop's separate letter layers travel as `shapes`, `guides` and `sp`. Export is built by `buildData()` / `buildScript()` and the three templates (`<script type="text/plain" id="jsx-ai">`, `jsx-id`, `jsx-ps`).
   - A feature that can be expressed through the existing fields needs **no** change to the templates. Prefer this.
   - Anything that adds a field or changes what a template does must update **all three** templates, and must be flagged to the owner **before you start**, because she tests each Adobe app by hand. Do it as its own commit so it can be reviewed and reverted on its own.
   - Other output formats (PNG, and anything added later) are separate code paths. They must never change what `buildData()` returns for the existing features.
2. **Variable fonts are used only as their named instances** (Light, Bold, Condensed Bold…), never as arbitrary axis values. This is what lets the Adobe apps find the same font by name. Do not add continuous `wght` / `wdth` / `opsz` sliders.
3. **No build step, no frameworks, no dependencies.** Plain HTML/CSS/JS. The tool must keep working when someone downloads it and opens it from disk in Chrome (`file://`), so no `fetch()` of local files; use `<script src>` for anything split out (like `demo-fonts.js`).
4. **UI language is English.** Keep the existing visual language: black on white, ruled grid, greys only for state, Helvetica stack, the existing `--s-*` sizes, the V3.1 title levels, option states and unit style. New controls reuse existing patterns (`.ctl` + range + `<output>`, `.opts` button groups, `bindRange` / `bindOpts`). Selection marks stay the corner-tick (crop-mark) style, in a neutral colour that never follows the user's colours.
5. **Determinism.** Layout randomness goes through `rnd(seed, unit, channel)`. Channels 0–6, 8 (outline) and 9 (boxes) are taken; **use 10 and up** for new random decisions. `Math.random()` may only be used to draw a new seed number when the user presses a reroll button, never inside layout or rendering. The same seed and settings must always give the same result.
6. **New settings** go into `DEFAULTS` and `SAVE_KEYS`. That makes them persist in localStorage *and* join undo/redo (`snapshot()` reads `SAVE_KEYS`). Anything with structure (objects, arrays) must also be normalised in `fixDeco()` so saved sessions from older versions, which lack the new keys or hold the old shape, still load. Call `record()` before changing state; pass a key for slider drags so a drag is one undo step. View-only state (panel widths, folds) stays out of `SAVE_KEYS`.
7. Update the **Help dialog** (`<dialog id="help">`) and **README.md** whenever behaviour the user sees changes.
8. **Never read or print `demo-fonts.js`.** It is ~600 KB of base64 on one line.

## Code map

Everything is in `index.html` (~3300 lines) plus `demo-fonts.js`. Line numbers drift, so find sections by their banners: `/* ---------------- name ---------------- */`.

- CSS at the top; app markup next (top bar, left panel, `#sheet` with `#deco` SVG under `#out` and `#selmarks` SVG over it, right panel `#side`); dialogs `picker`, `help`, `exp-ask`, `exp-help`; then the three `jsx-*` template blocks.
- Script banners, in order: state (`DEFAULTS`, `S`) → sfnt parsing → glyph ink measurement (`ink()`) → pool → **engine** (`rnd`, `applyCase`, `pickFrom`, `compute`, `evenGaps`, `alignLine`) → render (`render`, `layout`) → boxes and guides geometry (`decoShapes`, `guideLines`, `drawDeco`) → selection → hover info / selection info → right panel → panel UI (`bindRange`, `bindOpts`) → font sources → **export** (`buildData`, `buildScript`, `exportFor`) → persistence (`SAVE_KEYS`, `fixDeco`, `save`) → undo / redo (`snapshot`, `record`, `restore`) → zoom → panel widths → boot.
- `compute()` builds `S.lines`: one object per character (`st`, `size`, `jit`, `tr`, `va`, `bs`, plus rotation and stroke). Per-letter size, baseline or tracking changes belong there or in a pass right after it.
- `render()` draws the text as one HTML `<span>` per letter (family from `g.st.css`, variable instance from `g.st.fvs`) inside `#out`, then `layout()` measures every letter into `S.gmap` (`g.x`, `g.adv`, `g.y`, `S.lb[k]` baselines). Boxes, guidelines and all exports work from those numbers, in block coordinates (x from the text block's left edge, y down from the line's baseline). Rotation turns about the centre of the em box (`ROT_PIVOT`).
- `S.overrides` holds the user's manual per-letter edits (`style`, `va`, `fill`, `sw`, `rot`, `ra`, `ru`, `box`). Manual edits always win over automatic choices.
- Box style colours are currently grey ink percentages (0 white, 100 black) via `greyHex()` / `greyRGB()`.
- Terminology: "Seed" in the UI is the random-number seed (`S.seed`, typeable). Do not reuse that word for anything new without checking with the owner.

## How to work

- Work on a branch (e.g. `feature/png-export`), push it, and open a pull request. **Never push to `main`**: it is the live site, and the owner merges the PR herself after checking the feature.
- One roadmap step per commit (or a few small commits). Don't bundle features. **Do the steps one by one: finish a step, self-test, commit, then stop and report. Do not start the next step until the owner says so.**
- Commit messages, branch names, PR titles/bodies and code comments are in English.
- Test locally in Chrome: `python3 -m http.server 8000` and open http://localhost:8000 (localhost counts as a secure context, so installed-font access works). Check with the sample fonts and with at least two different installed families, and check from `file://` too.
- If a step touches the `.jsx` export, export AI and ID once yourself, open the `.jsx` in a text editor and check the data. Then **tell the owner to run the export in Illustrator, InDesign and Photoshop**: you can't run Adobe apps, she can. Say exactly what to look for.
- Existing saved sessions must keep loading: after a step, load a session saved with the previous version and confirm nothing breaks or moves.
- When a step's approach isn't clean, stop and discuss it with the owner before implementing.
- Keep comments short, matching the existing style.

## Roadmap: V4

V4 is a major release, so it ships the **basic** version of each feature. Everything listed under "Not in V4" waits for V4.x. Latin script only; Chinese/Latin mixing stays deferred. Do the steps in this order, because later ones depend on earlier ones.

### 1. Export as an image (PNG)
Mixweight has been built around editable export; the plain "give me a picture" case is missing.
- A **PNG** button in the top bar next to AI / ID / PS, same button pattern. A small options dialog (reuse the `exp-ask` dialog style): scale **1× / 2× / 4×**, **Transparent background** (off by default → white), and **Copy to clipboard** as an alternative to download (PNG through the async Clipboard API; if the browser refuses, fall back to a download and say so). Remember the last choices in localStorage.
- The image holds the text and the decoration (letter boxes, bands, paragraph band, outline letters, rotation), exactly as on the canvas. It never includes selection marks, the hover tag, zoom or other UI.
- **Include guidelines** checkbox in the same dialog (independent of the `.jsx` export's own checkbox). Step 3 adds **Include labels** next to it.
- Crop to the text block plus a margin (like the `.jsx` export's `m`), not to the screen.
- It must match the preview: right font, right named instance, right size, tracking and baseline shift on every letter, rotated and outlined letters included. Check how `g.st.css` and `g.st.fvs` reach the browser; if drawing on a 2D canvas cannot reproduce a variable instance faithfully, say so and propose an alternative before building.
- Does **not** touch `buildData()` or the `.jsx` templates.
- Check: mix two families, include rotated letters, outline letters, a band and boxes; export at 1× and 4×; compare against the screen. Transparent PNG over a dark background shows no white fringe.

### 2. Colour system (basic)
Today everything is black on white with grey-only decoration, and the selection style was chosen to leave room for colour.
- Colours are **RGB hex**. No CMYK, no gradients, no random text colour (random box colours are step 2b).
- What gets a colour: global **text colour**, **background colour** (canvas sheet; the PNG uses it unless Transparent is on), **box / band / paragraph-band fill and stroke**, **guideline colour**, and **selected letters' text colour** (an override like the existing ones, in the right panel). Outline letters stroke in their text colour.
- Greys keep working: boxes saved by v3.x hold numeric grey ink percentages (`fill`, `stroke`). Migrate them to the new representation in `normStyle()` / `fixDeco()` so old sessions look identical.
- A colour control reuses the existing right-panel patterns; include a **recent colours** row (last ~8 used) and the preset palettes below.
- **Preset palettes**: the owner supplies them as hex lists in `reference/palettes.md` (about eight, derived from her references; the reference images themselves are not kept in this public repo). Each preset has three roles, **text**, **background** and **block** (the colour for boxes and bands), plus a few extra colours that step 2b draws from. Keep presets in one `PALETTES` constant. Applying a preset sets text, background and block colours in one click (one undo step). Do **not** invent palettes: if `reference/palettes.md` is missing when you reach this step, build the mechanism with a single black-on-white preset and stop to ask.
- Selection corner-ticks and the hover tag stay neutral.
- Export impact (do as a **separate, final commit of this step, after confirming with the owner**): the `.jsx` templates currently carry no colour for text, and box fills/strokes are greys. Adding text colour per run and RGB for shapes and guidelines changes all three templates (Photoshop's separate letter layers included). Flag it first, make the canvas and PNG colour work complete before touching it, and then tell the owner exactly what to test in each Adobe app.
- The same commit adds an opt-in **Include background** checkbox (same style as the guidelines' Include in export, off by default): a rectangle in the background colour, the size of the text block plus margin (`w` × `h`), sent to the back and named `Mixweight background` (its own layer in Illustrator and InDesign like the guidelines; in Photoshop the new document is filled with the colour). Illustrator and InDesign have no canvas colour, so this is a real object in the user's document, which is why it is opt-in.
- Check: colour text, background, boxes and guidelines; undo/redo; reload; load a v3.2-era saved session; PNG export with and without Transparent.

### 2b. Random box colours from the palette
Reference: letter boxes in several colours under black text (the `POGO ALL NIGHT LONG` poster and the `Block Projects` strip in the owner's references).
- Letter boxes only. Bands and the paragraph band keep one colour. Text colour is not randomised (V4.x).
- In the Box group's random letter boxes (the existing ratio + Reroll), add **Box colour: Single / From palette**. Single is today's behaviour. From palette: each randomly placed box takes a colour from the active preset's colour list (block + extras), drawn with a new `rnd` channel (10) off `boxSeed`, so the same seed always gives the same colours. Reroll rerolls placement and colours together.
- A box colour the user set by hand always wins over the random one, like every other override.
- With no preset active, From palette is disabled with a short hint ("Pick a palette first"). Editing a palette is V4.x.
- New keys go into `DEFAULTS`, `SAVE_KEYS` and `fixDeco()`; sessions saved before load as Single.
- It exports as ordinary RGB box fills, covered by step 2's `.jsx` commit. If this step ships before that commit, say so in the PR.
- Check: scatter boxes at about 50% with a palette, reroll, undo/redo, reload (same colours), set one box by hand then reroll (it stays).

### 2c. Letter-box height: glyph or line
Reference: `POGO ALL NIGHT LONG`, where every letter sits on a column as tall as its line and the columns touch.
- New option in the Box group for letter boxes: **Height: Glyph / Line**. It is part of the box style, so it works per selection like the other box parameters.
- **Glyph** is today's behaviour (the box follows the letter's own ink). **Line**: every box in a line is as tall as that line's tallest letter (the same rule as a band's "Tallest letter") and as wide as the letter's advance width rather than its ink, so neighbouring boxes touch. Expand, corners, fill and stroke still apply, and a rotated letter's box still turns with it.
- Default Glyph; sessions saved before load as Glyph (handle it in `normStyle()`).
- It exports as the same rectangles; InDesign keeps the live corner options. If the current box geometry makes Line awkward, stop and discuss before building.
- Check: four lines in a heavy family, select all, add letter boxes with Line: a clean grid of columns; with Glyph, a session saved in v3.2 looks exactly as before.

### 3. Static labels
A fixed version of the hover info tag.
- Select letters, then attach a label to each selected letter. The label has the same content as the hover tag, one line per field, in this order: font family, style, size, baseline shift, tracking. The hover tag's look is the reference: stacked gray highlight boxes, small black uppercase underlined sans text (`reference/hover-tag.png`). Reuse `tagRows()` / `fillTag()` rather than duplicating the content logic.
- Per label, the user can switch each of the five lines on or off. The default is all five.
- Labels are **draggable** to any position; store the position as an offset from its letter, so the label follows the letter when text or layout changes.
- Remove a label from the right panel (single and all). Labels are state: they belong in `SAVE_KEYS`, undo/redo and `fixDeco()`.
- Labels draw above the text and decoration but below the selection marks.
- In the PNG dialog, add **Include labels**. Labels are **not** exported to the `.jsx` in V4 (that would change the templates); say so in Help.
- Check: attach labels to letters in two different fonts, drag them, change the seed (letters change, labels follow), turn lines off, export a PNG with and without labels, undo/redo.

### 4. Specimen (seed archive)
Save and share the whole look of a result as parameters only, with no font files. The UI word is **Specimen** ("Seed" stays the random number).
Do this in two parts, each its own commit; stop and report after part A.

**A. Specimen file**
- A specimen is a JSON object holding everything in `SAVE_KEYS` plus the manual per-letter overrides, the text, colours and labels, plus: a **specimen version** number (so old specimens keep loading in newer versions), the tool version, and the **names of the fonts it uses** (family and style names, plus PostScript names, as the `.jsx` export already matches them), not the font data. Optional metadata fields for later gallery use: `title`, `author`.
- UI: **Copy specimen** (text to clipboard), **Download specimen** (`.json`), and **Load specimen** (paste or choose a file). Loading is one undo step.
- Loading a specimen whose fonts the user doesn't have: list the missing fonts in a clear message, apply the specimen with the base style or default font standing in for them, and say that layout will differ because widths differ. A specimen made with the same fonts must reproduce the result exactly.
- Everything works offline from `file://`; no server, no links.
- Validate input defensively (a pasted specimen may be hand-edited or from a newer version): ignore unknown keys, normalise through the same path as saved sessions, never throw.
- Check: save a specimen, change everything, load it back: identical result. Load it with a font removed from the pool: the message is right and nothing crashes. Load a specimen made by the previous step's version.

**B. My specimens (personal gallery)**
- A **My specimens** list inside the tool: **Save current** (asks for a name), and each entry shows its name, date and a small thumbnail made with the PNG export from step 1. Click to load (one undo step), rename, delete.
- Storage is the browser's IndexedDB (localStorage is too small for thumbnails). It must degrade gracefully: if storage is unavailable or blocked, say so and keep everything else working.
- Stored data lives per browser and per origin: the online version and a downloaded offline copy do not share it, and clearing site data erases it. So include **Export all** (one `.json` holding every specimen) and **Import** (merge, never overwrite silently; ask on a name clash). Say this in Help.
- Entries use exactly the part A specimen format, so a file the user downloads can be imported and one in the list can be downloaded.
- Check: save several, reload the page, they are still there; delete and rename; Export all, clear the list, Import: identical entries and thumbnails; open the page from `file://` and from localhost.

### Not in V4 (V4.x or later)
- SVG export; CMYK; gradients; palette extraction from images; random text colour from the palette (random box colours are step 2b); palette editing.
- Static labels in the `.jsx` export (first as plain text lines without the grey highlight boxes, whose widths would not match in the Adobe apps).
- Type animation templates (its own release); box/grid pattern behind the text (V4.x candidate); a crop-mark (registration cross) decoration; a floating panel, only if the Color group outgrows the sidebars.
- A public gallery with user uploads (needs a backend; start with a hand-curated static gallery that opens specimens).

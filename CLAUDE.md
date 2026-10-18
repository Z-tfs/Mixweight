# Mixweight — notes for Claude Code

Mixweight mixes the named styles of installed (or dropped) fonts letter by letter, and exports the result as a `.jsx` script that rebuilds it as **live, editable text** in Illustrator, InDesign and Photoshop. Live at https://mixweight.pages.dev (Cloudflare Pages). The tool is currently at v3.2; the next release is **V4**.

The owner is a graphic designer, and Mixweight is a visual tool for designers, not an engineering project. Judge every change by how the type looks and whether it survives export, not only by whether the code runs.

## Hard rules

1. **The `.jsx` export must stay editable.** Nothing in it gets outlined or rasterised. Each letter run is `[text, fontIndex, size, baselineShift, tracking, rotation, strokeWidth]` (see `buildData()`); boxes, guidelines and Photoshop's separate letter layers travel as `shapes`, `guides` and `sp`. Export is built by `buildData()` / `buildScript()` and the three templates (`<script type="text/plain" id="jsx-ai">`, `jsx-id`, `jsx-ps`).
   - A feature that can be expressed through the existing fields needs **no** change to the templates. Prefer this.
   - Anything that adds a field or changes what a template does must update **all three** templates, and must be flagged to the owner **before you start**, because she tests each Adobe app by hand. Do it as its own commit so it can be reviewed and reverted on its own.
   - Other output formats (PNG, and anything added later) are separate code paths. They must never change what `buildData()` returns for the existing features.
2. **Variable fonts are used only as their named instances** (Light, Bold, Condensed Bold…), never as arbitrary axis values. This is what lets the Adobe apps find the same font by name. Do not add continuous `wght` / `wdth` / `opsz` sliders. **Never distort the letterforms**: no stretching, squashing or warping of glyphs; the font itself stays untouched (size, rotation and the named styles are the only ways a letter changes).
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
- **Include guidelines** checkbox in the same dialog (independent of the `.jsx` export's own checkbox).
- Crop to the text block plus a margin (like the `.jsx` export's `m`), not to the screen.
- It must match the preview: right font, right named instance, right size, tracking and baseline shift on every letter, rotated and outlined letters included. Check how `g.st.css` and `g.st.fvs` reach the browser; if drawing on a 2D canvas cannot reproduce a variable instance faithfully, say so and propose an alternative before building.
- Does **not** touch `buildData()` or the `.jsx` templates.
- Check: mix two families, include rotated letters, outline letters, a band and boxes; export at 1× and 4×; compare against the screen. Transparent PNG over a dark background shows no white fringe.

### 2. Colour system (basic)
Today everything is black on white with grey-only decoration, and the selection style was chosen to leave room for colour.
- Colours are **RGB hex**. No CMYK, no gradients, no random text colour (random box colours are step 2b).
- What gets a colour: global **text colour**, **background colour** (canvas sheet; the PNG uses it unless Transparent is on), **box / band / paragraph-band fill and stroke**, **guideline colour**, and **selected letters' text colour** (an override like the existing ones, in the right panel). Outline letters stroke in their text colour.
- The existing box style keeps everything except the two grey sliders: **Opacity**, **Expand**, **Corners** and **Stroke width** stay as they are and apply on top of any colour (Opacity covers fill and stroke together, as today). Only "Fill grey" and "Stroke grey" become colour controls. Opacity already travels in the `.jsx` export, so colour needs no new opacity field.
- **Blend mode** for boxes and bands, in the same Box style group next to Opacity: **Normal / Multiply / Screen / Overlay / Difference** (five, no more in V4; more modes are V4.x). Part of the box style, so it works per selection and for the paragraph band like the other parameters; saved sessions without it load as Normal (`normStyle()`). On the canvas it blends with what is behind the box (background colour, grid, other boxes), never with the text above it. The PNG must match the canvas, including with Transparent on (blend against nothing then looks like Normal; say so in the PNG dialog hint).
- Greys keep working: boxes saved by v3.x hold numeric grey ink percentages (`fill`, `stroke`). Migrate them to the new representation in `normStyle()` / `fixDeco()` so old sessions look identical.
- A colour control reuses the existing right-panel patterns; include a **recent colours** row (last ~8 used) and the preset palettes below.
- **Preset palettes**: `reference/palettes.md` holds eleven starting presets, sampled from the owner's reference posters (the images themselves are not kept in this public repo). They are a **style reference, not an exact spec**: soft and saturated pastels, black or tinted ink, paper-like grounds. You may tune a hex value for contrast or harmony, but keep each preset's character, and list every change in the PR so the owner can review the presets in the tool. Do not add presets without asking. Every preset has **text** and **background**; most also have a **block** colour (for boxes and bands) and some have **extras** that step 2b draws from; the two-colour presets have neither. Keep presets in one `PALETTES` constant. Applying a preset sets only the roles it has, in one click (one undo step). If `reference/palettes.md` is missing when you reach this step, build the mechanism with a single black-on-white preset and stop to ask.
- Selection corner-ticks and the hover tag stay neutral.
- Export impact (do as a **separate, final commit of this step, after confirming with the owner**): the `.jsx` templates currently carry no colour for text, and box fills/strokes are greys. Adding text colour per run, RGB for shapes and guidelines, and a blend mode per shape (the same way opacity is written today) changes all three templates (Photoshop's separate letter layers included). Flag it first, make the canvas and PNG colour work complete before touching it, and then tell the owner exactly what to test in each Adobe app.
- The same commit exports the background as the **bottom-most shape**: a rectangle in the background colour, the size of the text block plus margin (`w` × `h`), sent to the back and named `Mixweight background` (its own layer in Illustrator and InDesign like the guidelines; a shape layer at the bottom in Photoshop). No checkbox. It is written only when the background colour is not the default white, so an export made without touching the background stays identical to v3.2.
- Check: colour text, background, boxes and guidelines; each blend mode over a coloured background and over overlapping boxes, compared with the canvas; undo/redo; reload; load a v3.2-era saved session; PNG export with and without Transparent.

### 2b. Random box colours from the palette
Reference: letter boxes in several colours under black text (the `POGO ALL NIGHT LONG` poster and the `Block Projects` strip in the owner's references).
- Letter boxes only. Bands and the paragraph band keep one colour. Text colour is not randomised (V4.x).
- In the Box group's random letter boxes (the existing ratio + Reroll), add **Box colour: Single / From palette**. Single is today's behaviour. From palette: each randomly placed box takes a colour from the active preset's block colour and extras, drawn with a new `rnd` channel (10) off `boxSeed`, so the same seed always gives the same colours. Reroll rerolls placement and colours together.
- A box colour the user set by hand always wins over the random one, like every other override.
- With no preset active, or a preset without a block colour, From palette is disabled with a short hint ("Pick a palette with block colours first"). Editing a palette is V4.x.
- New keys go into `DEFAULTS`, `SAVE_KEYS` and `fixDeco()`; sessions saved before load as Single.
- It exports as ordinary RGB box fills, covered by step 2's `.jsx` commit. If this step ships before that commit, say so in the PR.
- Check: scatter boxes at about 50% with a palette, reroll, undo/redo, reload (same colours), set one box by hand then reroll (it stays).

### 2c. Letter-box fit: height and width
Reference: `POGO ALL NIGHT LONG`, where every letter sits on a column as tall as its line and the columns touch. A full-line grid alone looks too uniform, so height and width are **two independent choices**, and the box can hug the letter on either axis.
- Two options in the Box group for letter boxes, part of the box style so they work per selection like the other box parameters:
  - **Height: Glyph / Line**. Glyph: the box is as tall as the letter's own ink (today's behaviour). Line: as tall as the line's tallest letter (the same rule as a band's "Tallest letter").
  - **Width: Glyph / Advance**. Glyph: as wide as the letter's ink (today's behaviour). Advance: as wide as the letter's advance width, so neighbouring boxes touch.
- The four combinations give different looks: Glyph + Glyph is v3.2; Glyph height + Advance width gives touching boxes that follow each letter's own height (a ragged skyline); Line height + Glyph width gives tall slabs with gaps; Line + Advance gives the POGO grid of columns. Expand, corners, fill and stroke still apply to all of them, and a rotated letter's box still turns with it.
- Defaults Glyph and Glyph; sessions saved before load as Glyph / Glyph (handle both in `normStyle()`).
- It exports as the same rectangles; InDesign keeps the live corner options. If the current box geometry makes any combination awkward, stop and discuss before building.
- Check: four lines in a heavy family, select all, add letter boxes and try all four combinations (columns, ragged skyline, slabs, v3.2 look); with Glyph + Glyph, a session saved in v3.2 looks exactly as before.

### 3. Pattern behind the text (grid and friends)
A pattern drawn behind the type, part of the layout-aids group (see below). Basic version only.
- **Pattern type**, one at a time: **Grid** (square cells), **Dots**, **Crosses** (small plus marks at the cell corners), **Lines** (horizontal rules) and **Diagonal** (45° lines). Five types, no more in V4 (triangles, isometric, hex and graph paper with major lines are V4.x).
- Settings: on/off (off by default), type (default Grid), **Cell size** (pt), **Line weight** (pt; for Dots it is the dot diameter), **Colour** (from the colour system, default a light grey). Cell size is a slider the user can pull from very fine to coarse: **2 to 120 pt in 1 pt steps, default 16 pt** (40 pt or more is too coarse for type this size). Line weight 0.25 to 4 pt, default 0.5 pt. Keep very fine patterns cheap to draw (one SVG `<pattern>`, not thousands of elements).
- The pattern fills the frame (text block plus margin, the same frame the PNG crops to), starts at that frame's top-left corner so cells line up with the text block, and never runs over the whole canvas.
- Draw order, bottom to top: background colour, pattern, boxes and bands, text. Box blend modes act on the pattern too.
- It is part of the canvas and of the PNG. It is **not** exported to the `.jsx` in V4 (say so in Help).
- Settings are state: `DEFAULTS`, `SAVE_KEYS`, `fixDeco()`, undo/redo; sessions saved before load with the pattern off.
- Check: turn it on, try all five types at a few cell sizes, change the text (the frame and pattern follow), boxes and text stay above it, PNG export with and without a coloured background.

### 4. Registration marks
Crosshair marks at the four corners of the frame, as on the `POGO ALL NIGHT LONG` poster. Same group as the pattern.
- Settings: on/off (off by default), **Size** (pt), **Line weight** (pt), **Inset** from the frame's corner (pt), **Colour**.
- Marks sit at the corners of the same frame as the pattern and the PNG crop, so they are always inside the PNG.
- Canvas and PNG only; not exported to the `.jsx` in V4 (say so in Help).
- Check: on/off, resize, a coloured background, PNG export shows all four marks and nothing is cropped.

**Layout aids group.** To keep the left panel from growing, put the pattern and the marks in one group with the existing Guidelines (title: **Layout aids**), folded by default, each aid with its own switch. Keep the Guidelines wording and its Include in export checkbox exactly as they are.

### 5. Specimen (seed archive)
Save and share the whole look of a result as parameters only, with no font files. The UI word is **Specimen** ("Seed" stays the random number).
The personal gallery that was part of this step is parked for V4.x (see below).

- A specimen is a JSON object holding everything in `SAVE_KEYS` plus the manual per-letter overrides, the text, colours, pattern and marks, plus: a **specimen version** number (so old specimens keep loading in newer versions), the tool version, and the **names of the fonts it uses** (family and style names, plus PostScript names, as the `.jsx` export already matches them), not the font data. Optional metadata fields for later gallery use: `title`, `author`.
- UI: **Copy specimen** (text to clipboard), **Download specimen** (`.json`), and **Load specimen** (paste or choose a file). Loading is one undo step.
- Loading a specimen whose fonts the user doesn't have: list the missing fonts in a clear message, apply the specimen with the base style or default font standing in for them, and say that layout will differ because widths differ. A specimen made with the same fonts must reproduce the result exactly.
- Everything works offline from `file://`; no server, no links.
- Validate input defensively (a pasted specimen may be hand-edited or from a newer version): ignore unknown keys, normalise through the same path as saved sessions, never throw.
- Check: save a specimen, change everything, load it back: identical result. Load it with a font removed from the pool: the message is right and nothing crashes. Load a specimen made by the previous step's version.

### 6. Look presets (last step, gated)
One click sets a whole look from the settings that already exist, so it needs no new engine work and no change to the `.jsx` templates. Start this step only after steps 1 to 5 are merged and the owner says so; if she does not, it becomes V4.1.
- Four presets, kept in one `LOOKS` constant next to `PALETTES`, shown as a small row in the left panel with the existing option-button pattern. Working names (from the owner's demos): **Riso Block**, **Stacked Bands**, **Tone on Tone**, **Dark Glow**.
  - **Riso Block**: paper palette with three block inks, letter boxes on Multiply (Height Glyph, Width Advance), dots pattern, with Size range, Base jitter and Rotation jitter turned up so it does not look flat.
  - **Stacked Bands**: one band per line, each its own palette colour, stepped widths, boxes small or off, a darker band with light text, grid and registration marks. Modest size range.
  - **Tone on Tone**: blue and beige palette, beige bands, outline letters in the text colour, crosses pattern. Modest size range.
  - **Dark Glow**: dark palette, letter boxes on Screen (Height Line, Width Advance), grid and registration marks.
- A look sets only the style settings (palette roles, pattern, marks, box and band style, blend, jitter ranges). It never changes the text, the seed, the fonts or the manual per-letter overrides. Applying a look is one undo step.
- A look is a starting point: the user can change every setting afterwards. Looks are not saved state; only the settings they set are.
- If a look needs something awkward (for example creating one band per line with different colours), stop and discuss with the owner before building it.
- Check: apply each look to the same text, then to different text and a different font; undo; reload; the result matches the owner's demos in spirit (she checks it by eye).

### Not in V4 (V4.x or later)
- Static labels and the personal gallery (specs parked below); labels in the `.jsx` export (first as plain text lines without the grey highlight boxes, whose widths would not match in the Adobe apps).
- Grain / noise texture over the canvas (V4.x); auto-contrast text colour for selected letters (V4.x, black or white chosen from the box or band behind).
- SVG export; CMYK; palette extraction from images; random text colour from the palette (random box colours are step 2b); palette editing.
- Pattern and registration marks in the `.jsx` export.
- Type animation templates (its own release); a floating panel, only if the sidebars stop being enough (to be discussed with the owner first).
- A public gallery with user uploads (needs a backend; start with a hand-curated static gallery that opens specimens). A share link that packs a specimen into the URL.

## Parked for V4.x (specs kept, do not build in V4)

### Static labels
A fixed version of the hover info tag.
- Select letters, then attach a label to each selected letter. The label has the same content as the hover tag, one line per field, in this order: font family, style, size, baseline shift, tracking. The hover tag's look is the reference: stacked gray highlight boxes, small black uppercase underlined sans text (`reference/hover-tag.png`). Reuse `tagRows()` / `fillTag()` rather than duplicating the content logic.
- Per label, the user can switch each of the five lines on or off. The default is all five.
- Labels are **draggable** to any position; store the position as an offset from its letter, so the label follows the letter when text or layout changes.
- Remove a label from the right panel (single and all). Labels are state: they belong in `SAVE_KEYS`, undo/redo and `fixDeco()`.
- Labels draw above the text and decoration but below the selection marks.
- In the PNG dialog, add **Include labels**. The first version leaves labels out of the `.jsx` export (that would change the templates); say so in Help. Labels also join the specimen file.
- Check: attach labels to letters in two different fonts, drag them, change the seed (letters change, labels follow), turn lines off, export a PNG with and without labels, undo/redo.


### My specimens (personal gallery)
- A **My specimens** list inside the tool: **Save current** (asks for a name), and each entry shows its name, date and a small thumbnail made with the PNG export from step 1. Click to load (one undo step), rename, delete.
- Storage is the browser's IndexedDB (localStorage is too small for thumbnails). It must degrade gracefully: if storage is unavailable or blocked, say so and keep everything else working.
- Stored data lives per browser and per origin: the online version and a downloaded offline copy do not share it, and clearing site data erases it. So include **Export all** (one `.json` holding every specimen) and **Import** (merge, never overwrite silently; ask on a name clash). Say this in Help.
- Entries use exactly the part A specimen format, so a file the user downloads can be imported and one in the list can be downloaded.
- Check: save several, reload the page, they are still there; delete and rename; Export all, clear the list, Import: identical entries and thumbnails; open the page from `file://` and from localhost.

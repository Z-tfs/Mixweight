# Mixweight

Mix the weights of your installed variable fonts letter by letter, then send the result to Illustrator, InDesign or Photoshop as live, editable text.

**Open it:** https://mixweight.pages.dev (Chrome or Edge)

## What it does

- Reads the fonts installed on your computer (Chrome / Edge font access), or takes dropped `.ttf` / `.otf` / `.ttc` files. Variable fonts are split into their named instances.
- Mixes styles across one or more families: mix amount, distribution (even, extremes, lean heavy, lean light, wave with adjustable period, ramp light → heavy across the text or per line, alternate light / heavy), unit (letter, fragment, word).
- Family share of mix: set each family's % of the mixed letters; the other families adjust so the total stays 100%. A family with nine styles no longer has to drown one with two.
- The base style is marked in the pool and must be one of the styles switched on. Pick it from the Base menu, or right-click any style: Set as base style, or Add to mix + set as base for a style that is off. Either is one undo step.
- Click a family's name in the pool to fold the family to that one row (the Collapse all / Expand all button under the Font pool title does every family). The folded row still shows the family's share of mix, and says so when it holds the base style. Folding is remembered and isn't an undo step.
- Case: original, all caps, all lower, title case, sentence, random. Selected letters can also be set to All caps or All lower on their own (right panel, Letter).
- Size range, baseline, tracking and rotation jitter (each letter turns a random amount from the seed; letters you rotated by hand keep their angle), leading, line alignment.
- Even gaps: where neighbouring letters come from different fonts, nudge the tracking so the gap sits halfway between the two fonts' own spacing. Exported as tracking.
- Match size: scale each font so its x-height or cap height matches the base style, so mixed families look the same size. Exported as per-letter point size.
- Letter align: baseline, or geometric top / middle / bottom measured from each glyph's actual outline.
- Click or drag across letters to change their font, weight or alignment by hand. A drag can start on empty canvas too.
- Right panel: select letters and it opens with their font, size, shift and tracking at the top (the same look as the hover tag), then weight, align, rotation, fill and boxes. It keeps its scroll place, so a new selection lands on the group you were using. With nothing selected it folds to a narrow Selection strip and the canvas takes the room; Close › folds it while letters stay selected, until you select other letters, and the strip's ‹ opens it.
- Hover info: point at a letter to see its family, style, size, shift and tracking. On by default; switch it off with Hover info in the bar above the canvas. Canvas only.
- Boxes: boxes per letter (following its ink, size and shift, and its rotation), a straight band under a selection (as tall as the tallest letter, or stepping with each letter; adjustable height), or a band under every line (one color, or **Per line from palette**: each line takes the next of the palette's block and extra colors). Fill and stroke color, stroke weight, opacity, blend mode (Normal, Multiply, Screen, Overlay, Difference), Expand X / Y (wider or taller than the ink, or narrower with negative values), Move X / Y (shift the box right / up; a rotated letter's box moves along the letter's own axes; all four snap to 0 near the tick at 0), letter-box Height (Glyph: the letter's ink, or Line: the line's tallest letter) and Width (Glyph: the letter without tracking, or Advance: up to the next letter, so boxes touch; Line + Advance gives a grid of columns), and round / bevel / inverse corners, all four together or each on its own (each corner in a 2 × 2 grid like the box: type the radius or drag the corner icon). Random letter boxes by ratio + reroll. Two Box style editors: the left panel's (folded under Random letter boxes) styles the paragraph band or every letter box; the right panel's styles the boxes and bands on the selected letters. A letter box styled in the right panel, or with its fill set by hand, keeps its style when the left editor or Reroll boxes changes the others. A letter can have a letter box and a band at once; when the selection has both, Box style's **Letter boxes / Band** switch picks which one you are styling. A blend mode mixes the box with the background and the boxes under it, never with the text.
- Color: No color for box fill, box stroke and the background (a background with no color gives a transparent PNG; a box with no fill exports with its stroke only). RGB hex for the text, the background, boxes and bands (fill and stroke), the guidelines, and selected letters (their own text color; outline letters stroke in it). Click a swatch for the system picker or type a hex value; the last eight colors used sit under each control, and with a palette active its colors sit in a row above them. Twelve preset palettes (eleven sampled from posters, plus a dark one) set text and background in one click, plus the box and band fill when the palette has a block color; click the active palette again to roll a new color match from its colors (readable text, a block that shows on the background). The Color group (text, background, palettes) is in the left panel and applies to the whole design; the palette list folds to a row of chips. Clicking a small swatch makes it the current color. Black on white goes back. Random letter boxes can take their colors **From palette**: each box gets the active palette's block color or one of its extras, picked by the seed, so Reroll boxes rerolls placement and colors together and the same seed always gives the same colors; a box colored by hand keeps its color and stays put. The background fills the text block plus a margin, the area the PNG crops to. Sessions from v3.x keep their greys. Colors show on the canvas, in the PNG and in the `.jsx` export.
- Random outline (left panel, Style tab): Amount + Reroll outlines sets a share of letters to outline only, with a Stroke weight. In the right panel, Fill sets selected letters solid or outline, with their own stroke weight.
- Paragraph band and Random letter boxes are in the left panel's Style tab too.
- Rotation: angle (slider or number) and random jitter + reroll for the selected letters, each turning on its own centre.
- Layout aids (left panel, folded until you open it): Guidelines: baseline, x-height, cap height and ascender of the base style, each on its own switch. Tick **Include in export** in the same group to export them.
- Decoration (left panel, under Layout aids):
  - Pattern: Grid, Dots, Crosses (plus marks at the cell corners), Lines (horizontal rules) or Diagonal (45°), behind the boxes and text, filling the text block plus a margin (the PNG's crop) from its top-left corner. Cell size 2–120 pt, line weight 0.25–4 pt, and a color; Dots have a Dot size of 5–100% of the cell instead, so they scale with it. Box blend modes mix with it. Exported to the `.jsx` as editable vectors on its own layer (a pattern with more than 4000 pieces is left out; use a larger cell or PNG).
  - Registration marks: crosshairs (a cross and a circle) at the four corners of the same area, with Size, Line weight, Inset from each corner and Color. They are always inside the PNG. Exported to the `.jsx` as editable vectors on its own layer.
- Seed: every random choice comes from it. Reroll for a new one, or click the seed number to type one in and get an earlier result back.
- Left panel tabs: **Type** (font pool, text, mix, type, letter align, jitter) and **Style** (look, color, paragraph band, random letter boxes, decoration, random outline, layout aids). The open tab is remembered.
- Clear: the one above the left panel resets everything you can set in the left panel, on both tabs, to its default: the styles in the mix, share of mix and base style, case, mix, type, letter align, jitter, colors and palette, guidelines (and Include in export), pattern and registration marks, paragraph band, random letter boxes (removing the boxes they placed) and random outline. Your text, the loaded fonts and the seed stay; Clear edits in the right panel removes every edit made on selected letters (letter changes, boxes, bands) and keeps the left-panel settings and the boxes Random letter boxes placed. Both undo.
- Panel widths: drag the rule between a panel and the canvas to resize it (left 280–560 px, right 260–520 px, and the canvas keeps at least 360 px); double-click the rule for the default width. Widths are remembered. The text's layout doesn't change with the canvas: in Fit mode the zoom follows the canvas, otherwise the zoom stays put.
- Folding groups: click a group title in the left panel to fold it to its title row (▸) and again to open it (▾). The summary stays visible while it is folded. All groups start open; each one's state is remembered, and folding isn't an undo step. Folding Font pool hides every family at once; a family's own name still folds just that family.
- Group titles: the right side of each title row sums up the group's current settings (for example `70% · Even · Letter` for Mix, `Off` for Jitter at zero) and updates as you change them. The grey note under a group is folded away; click the **?** next to the group title to show it, again to hide it.
- Look (left panel): one click sets a whole look from existing settings (palette, paragraph band, random letter boxes, pattern, registration marks), as one undo step. It only sets colors and decoration: text, seed, fonts, mix, type settings, jitter, outline and your letter edits stay. **Riso Block** (paper palette, letter boxes in its inks on Multiply touching side by side, dots), **Stacked Bands** (a band per line in the palette's colors, grid and registration marks), **Tone on Tone** (blue and beige, beige bands, crosses) and **Dark Glow** (dark palette, line-tall columns on Screen, grid and marks).
- Specimen (top bar): keep or share a whole look as settings only, with no font files: the text, every setting, letter edits, the pool and base style, and the names of the fonts it uses (family, style, PostScript name). **Copy specimen** (clipboard) or **Download specimen** (`.json`, with an optional title and author); **Load pasted** or **Choose file** to load one, as one undo step. With the same fonts it reproduces the result exactly. Fonts that aren't loaded are listed, the base style or your other fonts stand in, and the layout differs because their widths differ. A specimen from an older version loads with the newer settings at their defaults; unknown or broken values are ignored. Works offline from `file://`.
- ⌘Z / ⇧⌘Z undo and redo.

## Export

Each AI / ID / PS export is a `.jsx` script. Run it inside the app and it rebuilds the text there as live type, with every letter's font, style, size, baseline shift, tracking, rotation and outline already set. Nothing is outlined.

Boxes come along as editable vector shapes in a group under the text: InDesign rectangles keep live corner options (rounded, bevel, inverse rounded); Illustrator and Photoshop get the exact path. Tick **Include in export** under Guidelines to add the guidelines as plain lines on their own layer (Illustrator, InDesign) or group (Photoshop), named `Mixweight guidelines`. InDesign reuses that layer on later exports, and renames a `Mixweight guides` layer left by an older export instead of adding a second one.

Export from **AI**, **ID** or **PS** in the top bar. The first time, Mixweight asks whether you want the how-to too: **Script + how-to (.txt)** also saves `mixweight-illustrator-howto.txt` (or `-indesign-`, `-photoshop-`) with the steps below for that app and the After running notes; **Just the script** saves the `.jsx` alone. Tick **I know what a .jsx is. Stop asking.** and that choice is used from then on. The **×** (or Esc) closes the question without exporting. The **?** next to Export has these steps and an **Ask me again** button. Chrome may ask once whether the page can download multiple files; allow it to get both.

### PNG

**PNG** in the top bar saves a picture instead of a script: the text, its boxes, bands, outline and rotated letters, exactly as on the canvas, cropped to the text block plus the same margin as the `.jsx` export. Selection marks, the hover tag and zoom are never in it. Choose **Scale** 1× (one pixel per point), 2× or 4×, **Transparent background** (off: white), and **Include guidelines** (the guidelines that are switched on; separate from the `.jsx` checkbox). **Download PNG** saves `mixweight.png`; **Copy to clipboard** puts the image on the clipboard, and if the browser refuses, it is downloaded instead. The choices are remembered. The PNG is a flat image, not editable text. It needs Chrome or Edge; Safari can't draw it.

### Illustrator

1. Open the document you want the text in. If none is open, the script makes a new one.
2. **File › Scripts › Other Script…** (⌘F12).
3. Pick `mixweight-illustrator.jsx`. The text lands in the middle of the active artboard, selected.

### InDesign

InDesign only runs scripts from its Scripts panel, so there is one extra step the first time.

1. **Window › Utilities › Scripts**.
2. Right-click the **User** folder › **Reveal in Finder**.
3. Drop `mixweight-indesign.jsx` into that folder.
4. Double-click it in the Scripts panel. The text goes on the current page in a frame that sizes itself to the text; one ⌘Z undoes the whole thing.

Later exports have the same file name: replace the file in that folder and double-click again.

### Photoshop

1. **File › Scripts › Browse…**
2. Pick `mixweight-photoshop.jsx`. A new 72 ppi document opens with one editable type layer you can drag into your comp.

### After running

- The type is fully editable. Change words or fonts as usual.
- If the app says some fonts weren't found, they aren't installed on that computer; those letters fall back to the app's default font. Install the font, select those letters and reapply it. The sample fonts (Inter, Lora) only match if they are installed.
- Fonts are matched by family and style name, then PostScript name. Variable fonts use their named instances (Light, Bold…), so the app shows what the preview shows.
- Layers: the `.jsx` puts each part on its own layer, bottom to top: Mixweight background, pattern, boxes, marks, text, guidelines (in Photoshop as shape layers, the text layers in a Mixweight text group).
- Colors come along: text color per letter, RGB box and band fills and strokes (neutral greys stay greys: a Black tint in InDesign), blend modes, and the guideline color. InDesign adds each RGB color as a swatch named `Mixweight #RRGGBB`. A background that isn't white becomes a rectangle the size of the text block plus margin, at the very back, named `Mixweight background`: on its own layer in Illustrator and InDesign, the bottom shape layer in Photoshop. With a white background nothing extra is added.
- Outline letters are live text with no fill and a stroke in the letter's color. Rotated letters use character rotation, which Illustrator and InDesign apply about the centre of the letter's em box; Mixweight rotates about the same point, so boxes stay with their letters.
- Photoshop has no per-letter rotation or outline inside one type layer, so those letters become their own type layers (outline as a layer-style stroke, fill at 0%), and the main layer keeps a space tracked to the same width. Everything is still live type.
- Top / middle / bottom letter alignment is written as baseline shift values worked out for those exact glyphs. If you change a letter or its font in the app, export again from Mixweight to re-align.

## Files

- `index.html` — the whole tool. No build step, no dependencies.
- `demo-fonts.js` — the bundled sample fonts (Inter, Lora) as base64, loaded by `index.html` with a plain `<script src>`. Optional: without it the tool still works with installed or dropped fonts.

To use it offline, download `index.html` and `demo-fonts.js` into the same folder and open `index.html` in Chrome.
- `fonts/OFL.txt` — license for the bundled sample fonts (Inter, Lora; SIL Open Font License 1.1).

## License

Code: [MIT](LICENSE). Bundled sample fonts: SIL Open Font License 1.1, see [fonts/OFL.txt](fonts/OFL.txt).

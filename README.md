# Mixweight

Mix the weights of your installed variable fonts letter by letter, then send the result to Illustrator, InDesign or Photoshop as live, editable text.

**Open it:** https://mixweight.pages.dev (Chrome or Edge)

## What it does

- Reads the fonts installed on your computer (Chrome / Edge font access), or takes dropped `.ttf` / `.otf` / `.ttc` files. Variable fonts are split into their named instances.
- Mixes styles across one or more families: mix amount, distribution (even, extremes, lean heavy, lean light, wave with adjustable period, ramp light → heavy across the text or per line, alternate light / heavy), unit (letter, fragment, word).
- Family share of mix: set each family's % of the mixed letters; the other families adjust so the total stays 100%. A family with nine styles no longer has to drown one with two.
- The base style is marked in the pool and must be one of the styles switched on. Pick it from the Base menu, or right-click any style: Set as base style, or Add to mix + set as base for a style that is off. Either is one undo step.
- Click a family's name in the pool to fold its style list away (the Collapse all / Expand all button under the Font pool title does every family). Share of mix stays visible, and a folded family that holds the base style says so. Folding is remembered and isn't an undo step.
- Case: original, all caps, all lower, title case, sentence, random.
- Size range, baseline and tracking jitter, leading, line alignment.
- Even gaps: where neighbouring letters come from different fonts, nudge the tracking so the gap sits halfway between the two fonts' own spacing. Exported as tracking.
- Match size: scale each font so its x-height or cap height matches the base style, so mixed families look the same size. Exported as per-letter point size.
- Letter align: baseline, or geometric top / middle / bottom measured from each glyph's actual outline.
- Click or drag across letters to change their font, weight or alignment by hand. A drag can start on empty canvas too.
- Right panel: select letters and it opens with their font, size, shift and tracking at the top (the same look as the hover tag), then weight, align, rotation, fill and boxes. It keeps its scroll place for selection and for paragraph, so a new selection lands on the group you were using. Deselect and it folds away; the small handle on the canvas edge opens it for the whole paragraph: hover info, fill, the paragraph band and random letter boxes.
- Hover info: point at a letter to see its family, style, size, shift and tracking. On by default; switch it off in the right panel. Canvas only.
- Boxes: boxes per letter (following its ink, size and shift, and its rotation), a straight band under a selection (as tall as the tallest letter, or stepping with each letter; adjustable height), or a band under every line. Fill and stroke colour, stroke weight, opacity, blend mode (Normal, Multiply, Screen, Overlay, Difference), expand, and round / bevel / inverse corners, all four together or each on its own. Random letter boxes by ratio + reroll. A blend mode mixes the box with the background and the boxes under it, never with the text.
- Colour: RGB hex for the text, the background, boxes and bands (fill and stroke), the guidelines, and selected letters (their own text colour; outline letters stroke in it). Click a swatch for the system picker or type a hex value; the last eight colours used sit under each control. Eleven preset palettes set text and background in one click, plus the box and band fill when the palette has a block colour; Black on white goes back. The background fills the text block plus a margin, the area the PNG crops to. Sessions from v3.x keep their greys. Colours show on the canvas and in the PNG; the `.jsx` export does not carry them yet and draws box colours as greys of the same lightness.
- Fill: in the right panel. With nothing selected, Random outline + Reroll outlines sets a share of letters to outline only, with a Stroke weight; selected letters can be set solid or outline, with their own stroke weight.
- Rotation: angle (slider or number) and random jitter + reroll for the selected letters, each turning on its own centre.
- Guidelines: baseline, x-height, cap height and ascender of the base style, each on its own switch. Tick **Include in export** in the same group to export them.
- Seed: every random choice comes from it. Reroll for a new one, or click the seed number to type one in and get an earlier result back.
- Clear: the one above the left panel resets the left panel's look (case, mix, type, letter align, jitter, guidelines) to defaults; Clear edits in the right panel removes every right-panel edit (letter changes, boxes, bands, the paragraph band), sets Random outline and Stroke back to their defaults, and keeps the left-panel settings. Both undo.
- Panel widths: drag the rule between a panel and the canvas to resize it (left 280–560 px, right 260–520 px, and the canvas keeps at least 360 px); double-click the rule for the default width. Widths are remembered. The text's layout doesn't change with the canvas: in Fit mode the zoom follows the canvas, otherwise the zoom stays put.
- Folding groups: click a group title in the left panel to fold it to its title row (▸) and again to open it (▾). The summary stays visible while it is folded. All groups start open; each one's state is remembered, and folding isn't an undo step. Folding Font pool hides every family at once; a family's own name still folds just its styles.
- Group titles: the right side of each title row sums up the group's current settings (for example `70% · Even · Letter` for Mix, `Off` for Jitter at zero) and updates as you change them. The grey note under a group is folded away; click the **?** next to the group title to show it, again to hide it.
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
- Outline letters are live text with no fill and a black stroke. Rotated letters use character rotation, which Illustrator and InDesign apply about the centre of the letter's em box; Mixweight rotates about the same point, so boxes stay with their letters.
- Photoshop has no per-letter rotation or outline inside one type layer, so those letters become their own type layers (outline as a layer-style stroke, fill at 0%), and the main layer keeps a space tracked to the same width. Everything is still live type.
- Top / middle / bottom letter alignment is written as baseline shift values worked out for those exact glyphs. If you change a letter or its font in the app, export again from Mixweight to re-align.

## Files

- `index.html` — the whole tool. No build step, no dependencies.
- `demo-fonts.js` — the bundled sample fonts (Inter, Lora) as base64, loaded by `index.html` with a plain `<script src>`. Optional: without it the tool still works with installed or dropped fonts.

To use it offline, download `index.html` and `demo-fonts.js` into the same folder and open `index.html` in Chrome.
- `fonts/OFL.txt` — license for the bundled sample fonts (Inter, Lora; SIL Open Font License 1.1).

## License

Code: [MIT](LICENSE). Bundled sample fonts: SIL Open Font License 1.1, see [fonts/OFL.txt](fonts/OFL.txt).

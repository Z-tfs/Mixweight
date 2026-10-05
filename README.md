# Mixweight

Mix the weights of your installed variable fonts letter by letter, then send the result to Illustrator, InDesign or Photoshop as live, editable text.

**Open it:** https://z-tfs.github.io/Mixweight/ (Chrome or Edge)

## What it does

- Reads the fonts installed on your computer (Chrome / Edge font access), or takes dropped `.ttf` / `.otf` / `.ttc` files. Variable fonts are split into their named instances.
- Mixes styles across one or more families: mix amount, distribution (even, extremes, lean heavy, lean light, wave with adjustable period, ramp light → heavy across the text or per line, alternate light / heavy), unit (letter, fragment, word).
- Family share of mix: set each family's % of the mixed letters; the other families adjust so the total stays 100%. A family with nine styles no longer has to drown one with two.
- The base style is marked in the pool and must be one of the styles switched on.
- Case: original, all caps, all lower, title case, sentence, random.
- Size range, baseline and tracking jitter, leading, line alignment.
- Even gaps: where neighbouring letters come from different fonts, nudge the tracking so the gap sits halfway between the two fonts' own spacing. Exported as tracking.
- Match size: scale each font so its x-height or cap height matches the base style, so mixed families look the same size. Exported as per-letter point size.
- Letter align: baseline, or geometric top / middle / bottom measured from each glyph's actual outline.
- Click or drag across letters to change their font, weight or alignment by hand.
- Right panel: select letters and it opens with their font, size, shift and tracking at the top (the same look as the hover tag), then weight, align, rotation, fill and boxes. Deselect and it folds away; the small handle on the canvas edge opens it for the whole paragraph.
- Hover info: point at a letter to see its family, style, size, shift and tracking. On by default; switch it off in the right panel. Canvas only.
- Boxes: grey boxes per letter (following its ink, size and shift, and its rotation), a straight band under a selection (as tall as the tallest letter, or stepping with each letter; adjustable height), or a band under every line. Fill and stroke grey, stroke weight, opacity, expand, and round / bevel / inverse corners, all four together or each on its own. Random letter boxes by ratio + reroll.
- Outline: a ratio + reroll sets a share of letters to outline only; selected letters can be set solid or outline, with their own stroke weight.
- Rotation: angle (slider or number) and random jitter + reroll for the selected letters, each turning on its own centre.
- Guides: baseline, x-height, cap height and ascender of the base style, each on its own switch.
- Seed: every random choice comes from it. Reroll for a new one, or click the seed number to type one in and get an earlier result back.
- ⌘Z / ⇧⌘Z undo and redo.

## Export

Each export is a `.jsx` script. Run it inside the app and it rebuilds the text there as live type, with every letter's font, style, size, baseline shift, tracking, rotation and outline already set. Nothing is outlined.

Boxes come along as editable vector shapes in a group under the text: InDesign rectangles keep live corner options (rounded, bevel, inverse rounded); Illustrator and Photoshop get the exact path. Tick **Include guides** under Export to add the guides as plain lines on their own layer (Illustrator, InDesign) or group (Photoshop).

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

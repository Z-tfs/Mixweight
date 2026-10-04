# Mixweight

Mix the weights of your installed variable fonts letter by letter, then send the result to Illustrator, InDesign or Photoshop as live, editable text.

**Open it:** https://z-tfs.github.io/Mixweight/ (Chrome or Edge)

## What it does

- Reads the fonts installed on your computer (Chrome / Edge font access), or takes dropped `.ttf` / `.otf` / `.ttc` files. Variable fonts are split into their named instances.
- Mixes styles across one or more families: mix amount, distribution (even, extremes, lean heavy, lean light, wave with adjustable period, ramp light → heavy across the text or per line, alternate light / heavy), unit (letter, fragment, word).
- Case: original, all caps, all lower, title case, sentence, random.
- Size range, baseline and tracking jitter, leading, line alignment.
- Even gaps: where neighbouring letters come from different fonts, nudge the tracking so the gap sits halfway between the two fonts' own spacing. Exported as tracking.
- Match size: scale each font so its x-height or cap height matches the base style, so mixed families look the same size. Exported as per-letter point size.
- Letter align: baseline, or geometric top / middle / bottom measured from each glyph's actual outline.
- Click or drag across letters to change their font, weight or alignment by hand.
- ⌘Z / ⇧⌘Z undo and redo.

## Export

Each export is a `.jsx` script. Run it inside the app and it rebuilds the text there as live type, with every letter's font, style, size, baseline shift and tracking already set. Nothing is outlined.

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
- Top / middle / bottom letter alignment is written as baseline shift values worked out for those exact glyphs. If you change a letter or its font in the app, export again from Mixweight to re-align.

## Files

- `index.html` — the whole tool. No build step, no dependencies.
- `demo-fonts.js` — the bundled sample fonts (Inter, Lora) as base64, loaded by `index.html` with a plain `<script src>`. Optional: without it the tool still works with installed or dropped fonts.

To use it offline, download `index.html` and `demo-fonts.js` into the same folder and open `index.html` in Chrome.
- `fonts/OFL.txt` — license for the bundled sample fonts (Inter, Lora; SIL Open Font License 1.1).

## License

Code: [MIT](LICENSE). Bundled sample fonts: SIL Open Font License 1.1, see [fonts/OFL.txt](fonts/OFL.txt).

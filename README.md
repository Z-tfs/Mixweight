# Mixweight

Mix the weights of your installed variable fonts letter by letter, then send the result to Illustrator, InDesign or Photoshop as live, editable text.

**Open it:** https://z-tfs.github.io/mixweight/ (Chrome or Edge)

## What it does

- Reads the fonts installed on your computer (Chrome / Edge font access), or takes dropped `.ttf` / `.otf` / `.ttc` files. Variable fonts are split into their named instances.
- Mixes styles across one or more families: mix amount, distribution (even, extremes, lean heavy, lean light, wave), unit (letter, fragment, word).
- Case: original, all caps, all lower, title case, sentence, random.
- Size range, baseline and tracking jitter, leading, line alignment.
- Letter align: baseline, or geometric top / middle / bottom measured from each glyph's actual outline.
- Click or drag across letters to change their font, weight or alignment by hand.
- ⌘Z / ⇧⌘Z undo and redo.

## Export

Each export is a `.jsx` script that rebuilds the text inside the app, with every letter's font, style, size, baseline shift and tracking set. Nothing is outlined.

| App | Run the script from |
| --- | --- |
| Illustrator | File › Scripts › Other Script… |
| InDesign | Window › Utilities › Scripts (put the file in the User folder, then double-click) |
| Photoshop | File › Scripts › Browse… |

Fonts are matched by family and style name, then PostScript name. Variable fonts use their named instances, so the app shows what the preview shows.

## Files

- `index.html` — the whole tool, one self-contained file. It also works offline: download it and open it in Chrome.
- `fonts/OFL.txt` — license for the bundled sample fonts (Inter, Lora; SIL Open Font License 1.1).

## License

Code: [MIT](LICENSE). Bundled sample fonts: SIL Open Font License 1.1, see [fonts/OFL.txt](fonts/OFL.txt).

# Mixweight V4.1 text: line wrapping and typing on the canvas: brief

Two features for the released tool, built on `main`. Each one is a separate session and a separate step: finish it, self-test, commit, push, report with the preview links first, then **stop**. Start with step 1; step 2 builds on it.

## Rules for this brief

- CLAUDE.md "Hard rules" and "How to work" apply. Work on a new branch from `main`, open a PR into `main`, never push to `main`. After the owner merges, `main` gets merged into `beta` (see `mixweight-beta-brief.md`, "Keeping beta in step with main"; that brief lives on the `beta` branch).
- **Read only what you need.** Each step lists what to grep for. Read only those ranges of `index.html` (`Read` with offset and limit, or `sed -n`). Never read `index.html` top to bottom, never read `demo-fonts.js`.
- **No change to `buildData()`, `buildScript()` or the three `jsx-*` templates.** Both features must work through what the export already carries (one export line per line of `S.lines`). If a step seems to need a template change, stop and ask.
- Determinism: the same text, seed, settings and fonts always give the same lines. New `rnd` channels, if any, start at 12 on `main` (channel 12 is the palette roll; check `rnd(` calls first and take the next free one).
- New settings go into `DEFAULTS`, `SAVE_KEYS`, `fixDeco()` and `LIMITS` (Specimen loading). View-only state stays out of `SAVE_KEYS`.
- Saved sessions and specimens from V4 and v3.2 must load and look exactly as before.
- Update the Help dialog and README.md in the same commit.

## 1. Line wrapping and paragraph width

Today a line only breaks where the text has a return, so a long paragraph runs off the canvas. The owner wants long text to wrap so it **never runs wider than the canvas**, plus a **paragraph width** the user sets. No Adobe-style area text box: the `.jsx` keeps exporting fixed lines of live point text.

Where to look: `compute()` (the `engine` banner: it splits the text into `S.lines` at `\n`, and `lineLens` / `at.lineFrac` drive the position ramps), `evenGaps()`, `alignLine()`, `render()` and `layout()` (the `render` banner: `S.gmap`, `g.adv`, `S.lb`), `frame()` and `exportMargin()`, the Text and Type groups in the left panel markup (grep `id="text"`, `id="leading"`), the zoom banner (canvas width in pt at the current zoom), `remapOverrides()`, `fixDeco()`, `SAVE_KEYS`, `specValue()`.

What to build:
- A **Width** control in the left panel's Type tab (Text or Type group; pick the one that reads better and say which), existing patterns only: an `.opts` group **Off / Canvas / Fixed**, and for Fixed a `.ctl` range in pt with an `<output>` (`bindRange`).
  - **Canvas**: lines wrap at the width of the visible canvas, measured in pt at 100% zoom (zooming never rewraps; resizing the window or a panel does).
  - **Fixed**: lines wrap at the set width, independent of the window, so a Specimen reproduces exactly.
  - **Off**: today's behaviour.
- **Canvas handle**: while Width is Canvas or Fixed, a small handle on the right edge of the text block. Dragging it sets Fixed to that width (one undo step per drag, `record()` with a key). Draw it in the neutral selection color, the same family as the corner ticks; never in the user's colors; never in any export.
- **Breaking rules**: break at spaces; the space at a break is not counted in the width and does not start the next line. A word longer than the width breaks between letters. Returns in the text still break as now. Measure with the same advance widths and tracking that `layout()` uses, so what fits on screen fits in the export.
- **Wrapped lines are real lines.** Everything that works per line follows the wrapped lines: letter align, even gaps, a band's "Tallest letter", letter-box Height Line, paragraph band per line (and its Per line from palette colors), and the Stacked Bands look. The `.jsx` export gets one line per wrapped line through the existing data, so the Adobe apps show exactly the canvas lines.
- **No feedback loop.** A letter's style, size, shift and tracking must not depend on where the line wraps; otherwise wrapping changes widths and the wrap changes again. Keep the position ramps (`at.frac`, `at.lineFrac`) on the hard lines (the text's own paragraphs), so wrapping only regroups letters that were already chosen. Wrap once per render; never iterate.
- Overrides, bands and selection are keyed by character index, so they keep their letters; check that a band across a wrap point still draws on both lines.

Decisions the owner has made (November 2026):
- Default for new visitors: **Canvas** (text never wider than the canvas). Sessions and specimens saved before load as **Off**, so they look exactly as before.
- A Specimen saved with Width Canvas stores the resolved width in pt next to the mode, and on load uses it as Fixed, so the result is exact on another screen.
- Rotated letters and large jitter may still reach slightly past the width (wrapping uses advance widths). That is accepted, but the user must see it:
  - **Overflow mark**: when any letter's ink (rotation and shift included) reaches past the wrap width, draw the wrap edge as a thin line over the height of the text block and mark the part that sticks out, in the neutral selection color (never the user's colors), on the canvas only, never in the PNG or the `.jsx`.
  - With it, a short one-line note in the existing hint / toast style, for example "Some letters reach past the width (rotation or jitter).", with a **Don't show again** action. That choice turns off both the note and the mark. It is view state in `localStorage` (not in `SAVE_KEYS`, not in specimens). The Help dialog's Width section has a button to show the overflow reminder again.
  - Measure the overflow from the numbers `layout()` already has (`S.gmap`, ink boxes, rotation about `ROT_PIVOT`); no extra rendering.

Check: a long paragraph in two families wraps at Canvas and follows window and panel resizes but not zoom; turn Rotation jitter up until letters reach past the width: the mark and the note show, Don't show again hides both and survives a reload, Help brings them back, and neither appears in the PNG; Fixed by slider and by the handle; undo / redo; reload; a v3.2 and a V4 session look identical (pixel compare); Specimen round trip with Canvas and Fixed; bands, letter boxes with Height Line, paragraph band per line and Stacked Bands on wrapped text; PNG matches the canvas; export AI and ID once, open the `.jsx` and check each wrapped line is its own line and `var D` with Width Off is byte-identical to `main`. No Adobe test needed (no template change), but tell the owner to export one wrapped paragraph to each app and compare line breaks with the canvas.

## 2. Typing on the canvas

The user types straight into the text on the canvas, as with Illustrator's type tool, instead of only in the left panel's Text field.

Where to look: the `selection` banner (`selectRange()`, `paintSelection()`, the pointer handlers that select letters by click and drag; grep `dragging`), `#selmarks` and `drawSelMarks()`, the text field's input handler (grep `const text = $('#text')`), `remapOverrides()`, `record()` and the undo banner, the global keyboard shortcuts (grep `keydown`), and the wrapping from step 1.

What to build:
- **Double-click** a letter to start typing, with the caret at the nearer side of that letter. Double-click on empty canvas starts typing at the end of the text. Esc, or a click outside the text block, stops typing. Single click and drag keep selecting letters as now.
- While typing: a thin blinking caret drawn in `#selmarks` in the neutral selection color; arrows, Home / End, Shift + arrows to select text (shown with the existing selection marks), Backspace / Delete, Return for a new line, ⌘A selects all the text, ⌘C / ⌘X / ⌘V as plain text. The tool's own shortcuts are off while typing.
- **One text path.** Typing edits `S.text` through the same code as the Text field: overrides and bands follow their letters through `remapOverrides()`, the Text field stays in sync, and the result is the same as typing the same thing in the field. A good way: a hidden, focused textarea that mirrors `S.text`, whose selection drives the caret.
- **IME** (Chinese, Japanese input) works: show the composing text, commit on `compositionend`, no double letters.
- **Undo**: a burst of typing is one undo step (`record()` with a key, as for a slider drag); ⌘Z after leaving typing undoes it.
- With wrapping on, the caret moves through wrapped lines as they show, and typing rewraps live.
- Typing changes the random style of letters after the edit point, exactly as typing in the Text field does today (styles are picked per unit along the text). Say so in Help.
- Nothing of the typing UI appears in the PNG or the `.jsx`.

Check: double-click in the middle of a word and type, delete, Return; Shift-select and replace; paste; Chinese IME input; a letter edited in the right panel before the caret keeps its edit after typing before it; undo / redo of a burst; the Text field matches; Esc and click-away; from `file://`; old sessions load identically; `var D` unchanged for the same final text.

## Progress (update after each step)

- [x] 1 Line wrapping and paragraph width (branch `feature/line-wrap`; Width sits in the Type group, after Line align)
- [ ] 2 Typing on the canvas

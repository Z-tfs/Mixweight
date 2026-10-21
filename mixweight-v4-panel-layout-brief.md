# Mixweight V4: panel layout rework (brief)

V4 is not merged yet (PR #8, branch `claude/lucid-ride-wf1xim`). The owner wants the V4 interface reorganised before release, because the panels have become overwhelming. Work on that same branch. Do not open another branch or PR. `TOOL_VERSION` stays `'4.0'`.

This is a **UI-only** change: where things live, what is folded by default, and when a panel shows. No feature is added, removed or changed.

## Rules for this brief

- Follow CLAUDE.md "How to work": one step at a time. Finish a step, self-test, commit, push, report with the preview links first, then **stop**. Do not start the next step until the owner says so.
- Do **not** change `buildData()`, `buildScript()`, the three `jsx-*` templates, `DEFAULTS`, `SAVE_KEYS` or `fixDeco()`. If a step seems to need one, stop and ask.
- Move markup, do not rewrite controls: keep every control's `id` and its `bindRange` / `bindOpts` wiring. New view state (active tab, panel open) stays out of `SAVE_KEYS`, like the existing folds.
- Keep the visual language (CLAUDE.md hard rule 4). The only new UI atoms are the two-tab strip (active tab inverted, black with white text) and the closed-panel strip. Style them with existing tokens.
- Do not read `demo-fonts.js`. Do not open images in `reference/` (this brief names none).
- Each step says which part of the tool it changes and what to grep for. Grep for those ids and names and read only that range of `index.html`, never the whole file. Line numbers drift; ids and banners don't.
- For the rules that stay as they are (export, state, determinism, visual language), see CLAUDE.md "Hard rules"; this brief doesn't repeat them.
- This brief **replaces** these placement decisions in CLAUDE.md: "Look and Color sit below Jitter in the left panel", "The Color group lives in the left panel, after Look", "Pattern and registration marks are in their own left-panel group, Decoration, under Layout aids", and the Layout aids group note in roadmap step 4. Update CLAUDE.md in Step 7.

## Target layout

The owner's mockups are attached to the chat as screenshots: "3" (nothing selected) and "3b" (letters selected). In words:

**Left panel = settings for the whole text.** Two tabs at its top: **Type | Style**. The active tab is remembered (view state).
- **Type tab:** Font pool (family style lists folded by default, Share of mix stays visible, Base row), Text, Mix, Type (Letter align becomes its last row), Jitter (folded by default).
- **Style tab:** Look, Color (palette list shown as a compact row of chips by default), Paragraph band, Random letter boxes (with its Box style), Pattern, Registration marks, Random outline.

**Right panel = settings for the selected letters only.** Reroll / Reset / Deselect, Letter, Rotate, Fill, Box (letter boxes Add / Remove, band Add / Remove, and Box style for the selection). No whole-text settings in it.
- With no selection it is closed: a 36px strip with a vertical "Selection" label and a ‹ button; the canvas takes the space.
- It opens by itself when the selection goes from none to some, and closes when the selection becomes empty. Closing it by hand while letters are selected keeps it closed until the next new selection.

**Toolbar (the bar with Undo / Redo / Reroll / Seed / Zoom):** gains **Hover info** (on / off) and **Guides**. The **Layout aids** group goes away.

**Clear / Clear edits (owner's decision after Step 1):** Clear above the left panel resets everything that can be set in the left panel, on both tabs (done in `075b191`: font pool mix, colors and palette, layout aids and decoration included; the text, loaded fonts and seed stay). When a group moves into the left panel, its settings join Clear and leave Clear edits. Clear edits keeps only the selection's edits. Update their tooltips and Help wording to match.

**Font pool folding (owner's decision after Step 1):** a folded family, by its own title or by Collapse all / Expand all, folds to its title row with Share of mix hidden; the row shows its share (done in `075b191`).

## Steps

### 1. Left panel tabs
Add the Type | Style tabs. Put the groups where "Target layout" says, without changing their contents. Layout aids moves to the bottom of the Style tab for now. Letter align stays a separate group for now.
Where: the sub-bar (grep `id="clear-left"`), the left panel (grep `<aside class="rail"`), the boot code (grep `setupGroupFolds`).
Check: both tabs show the right groups; the tab choice survives a reload; every control still works (spot-check one of each kind: range, option group, color, palette).

### 2. Global groups leave the right panel
Move Random outline (today the "Fill" group in the paragraph mode), Paragraph band and Random letter boxes (today "Letter boxes" in the paragraph mode) from the right panel into the Style tab. Rename the left-panel title "Letter boxes" to **Random letter boxes**. The Box style editor stays where it is for now. Hover info stays for now.
Their settings join the left Clear and leave Clear edits (see "Clear / Clear edits" above).
Where: the right panel's paragraph groups (grep `id="side"`, then the titles `Fill`, `Paragraph band`, `Letter boxes` inside it), the Style tab (grep `data-tab="style"`), the two Clear handlers and their key lists (grep `clear-left`, `clear-ov`, `LEFT_KEYS`, `RIGHT_KEYS`).
Check: with nothing selected, those three groups work from the Style tab exactly as before; undo / redo; a v3.2 session loads identical.

### 3. Box style for two scopes (propose first, then build)
Today one Box style editor (`#bx-ed`) is switched between targets: Paragraph band / Letter boxes with nothing selected, Letter boxes / Band with a selection. Target: one editor in the Style tab, under Random letter boxes (Paragraph band and Random letter boxes keep sharing it through the existing Paragraph band / Letter boxes switch), and a second one in the right panel's Box group for the selection (existing Letter boxes / Band switch). Both can be on screen at once.
Ids are unique and `bindRange` binds by id, so two live copies are not trivial. **Stop and describe your approach first** (for example a factory that builds the editor with an id prefix, or moving one element between two parents) and wait for the owner's go-ahead before building.
Where: the Box style editor (grep `id="bx-ed"`, then `bx-` in the script for its wiring), `bindRange` / `bindOpts` (grep `function bindRange`).
Check: edit the global style and the selection style in turn, with letters selected; undo / redo; blend, expand / move and corners work in both; a v3.2 session loads identical.

### 4. Selection panel behaviour
Right panel holds only selection content (remove the paragraph mode and its "Paragraph" title). Add the closed strip, auto open and close, and the ‹ / Close › buttons as described above.
Where: the right panel (grep `id="side"`, `side-close`, `id="handle"`), the code that shows and hides it (grep `side.open`, `paintSelection`).
Check: select, deselect, shift / ⌘ extend, close by hand, select again; the canvas resizes; panel widths (`--lw`, `--rw`) are unchanged.

### 5. Toolbar: Hover info and Guides
Hover info becomes an on / off toggle in the toolbar (same state key as today). Guides: the setting has four lines, a color and the "Include in export" checkbox, so a single toggle is not enough. **Stop and propose** how it should look (for example a small popover from a Guides button, or a folded group at the end of the Style tab) before building, and keep the wording of Guidelines and "Include in export" exactly as it is. Then remove the Layout aids group.
Where: the toolbar (grep `id="zoom-fit"`), hover info (grep `hoverInfo`), the Layout aids group (grep `<span>Layout aids</span>`, `id="guides"`, `exp-guides`).
Check: hover tag on / off; each guideline line; guide color; Include in export still reaches the `.jsx` (compare the export data with the guides on).

### 6. Defaults and tidy
Apply these defaults only when nothing is saved for that fold: Font pool families folded; palettes compact (`S.palsOpen` view state); Jitter folded; Pattern, Registration marks and Paragraph band show only the title and Off / On while Off, and reveal their controls when On. Move Letter align into the Type group as its last row. Update every group's summary text on the right side of its title row so it still reads correctly.
Where: the fold state (grep `S.collapsed`, `palsOpen`, `groupsClosed`, `setupGroupFolds`), the group summaries (grep `summary('sum-`), the Letter align group (grep `<span>Letter align</span>`).
Check: first-run view (clear localStorage) shows the compact defaults; a user's saved fold choices are not overridden.

### 7. Docs and wrap-up
Where: the Help dialog (grep `<dialog id="help"`), README.md, CLAUDE.md "V4 status".
Update the Help dialog (Group titles, Color, Layout aids and Decoration, Look sections and anything else that names a place), README.md, and CLAUDE.md (the V4 status paragraph, the replaced decisions listed above, and the Open items line). Add every new commit to the PR description table with its fixed preview link. Do not bump `TOOL_VERSION`.

## Checks for every step

Run the checks CLAUDE.md lists under "Testing that has worked in the cloud container". Because no export code changes, also confirm the `var D = …` data for AI, ID and PS is byte-identical to the previous commit, and say so in the report. No Adobe test is needed unless a step ends up touching a template, which this brief does not allow without asking first.

## Not in this brief

Changing panel widths, a Look strip under the canvas, new Looks, a tabbed single-panel layout, mobile layout, and any new controls. Park ideas in your report under "V4.1 ideas"; do not build them.

## Progress (update after each step)

- [x] 1 Left panel tabs
- [x] 2 Global groups leave the right panel
- [x] 3 Box style for two scopes
- [x] 4 Selection panel behaviour (Hover info moved to the toolbar here, since its group was the last thing in the paragraph mode)
- [x] 5 Toolbar: Hover info and Guides (option A: Guides button with a popover; left Clear no longer resets guides)
- [ ] 6 Defaults and tidy
- [ ] 7 Docs and wrap-up

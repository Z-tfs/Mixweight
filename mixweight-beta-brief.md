# Mixweight beta ("Backstage"): brief

The owner is not ready to take V4 live (merging PR #8 deploys to main), so V4 stays unmerged. The beta is built **on top of the V4 branch** and gets its own fixed preview link through Cloudflare Pages.

Beta holds the bigger, rougher additions: motion templates, My specimens, a right-click panel on selected letters, and a fuller hover info. A small entry called **Backstage** leads from the stable page to the beta.

## Start condition

The V4 panel rework (`mixweight-v4-panel-layout-brief.md`) is finished, and branch `beta` already exists. It was created from the V4 branch (`claude/lucid-ride-wf1xim`) and contains this brief.

- Check out `beta` and work there. Cloudflare Pages serves the branch at `https://beta.mixweight.pages.dev`; confirm it from the deploy and report it.
- Open one **draft** PR `beta` into `claude/lucid-ride-wf1xim` titled "Mixweight Backstage (beta)". Never merge it, never push to `main`. The owner decides what merges where.
- `TOOL_VERSION` becomes `'4.1-beta.1'` in step 1; bump the last number once per step.

## Rules for this brief

- Follow CLAUDE.md "How to work": one step at a time. Finish a step, self-test, commit, push, report with the preview links first, then **stop**. Do not start the next step until the owner says so.
- **Read only what you need.** Follow CLAUDE.md "Briefs": each step lists what to grep for. Grep for it and read only that range of `index.html` (`Read` with offset and limit, or `sed -n`). Never read `index.html` from top to bottom, never re-read a file you just edited, and do not read `CLAUDE.md`, `README.md` or other briefs beyond the section a step names.
- CLAUDE.md hard rules all apply. Especially: no build step, no dependencies, works from `file://` in Chrome; variable fonts only as named instances (no continuous wght / wdth / opsz, no stretching or warping glyphs, no gradients); deterministic randomness (same seed and settings, same result; new `rnd` channels start at 13); UI text in English with American spelling; keep the visual language (black on white, ruled grid, greys for state, corner-tick selection).
- New settings go into `DEFAULTS` and `SAVE_KEYS` and are normalised in `fixDeco()`. View-only state (tabs, popovers, playing, open panels) stays out of `SAVE_KEYS`.
- **No step in this brief may change `buildData()`, `buildScript()` or the three `jsx-*` templates.** Motion is a video output only; the `.jsx` export always exports the static, un-animated state. If a step seems to need a template change, stop and ask.
- Every commit must leave old saved sessions (from V4 and v3.2) loading identically.
- Do not read `demo-fonts.js`. Do not open images in `reference/` unless a step names a specific file. Motion references, if the owner adds any, live in `reference/motion/` and will be named in the step.
- Everything a third person sees (commits, comments, PR text, UI text, README) is written in English.

## Steps

### 1. Skeleton and Backstage entry
Own commit, touching only what the entry needs, so the owner can later cherry-pick it to `main` on its own.

Where to look: the top bar markup and its CSS, `<dialog id="help">`, the `boot` banner, `TOOL_VERSION`.

- Constants near `TOOL_VERSION`: `BETA_URL` (empty string = entry fully hidden) and `FEEDBACK_URL`. The owner will give both values; use placeholders until then (`BETA_URL` will be `https://beta.mixweight.pages.dev`).
- Entry on a non-beta host (hostname not starting with `beta.`), shown only when `BETA_URL` is set:
  - A small button fixed at the bottom-right, label **Backstage**, drawn with existing tokens (white fill, black rule, inverts on hover).
  - A popup after the page has been **visible for 60 seconds** (count visible time only, pause when the tab is hidden). Non-modal small card anchored above the button, asking whether to try the beta features, with two actions: open the beta (new tab) and Not now. Never shown while the user is dragging, typing in a field, or has a dialog open. Shown at most once per browser: remember dismissal or use in `localStorage` (view state, not in `SAVE_KEYS`). Esc closes it.
- On the beta host (hostname starts with `beta.`): no popup. Show a small "Beta" mark in the toolbar and a feedback link (`FEEDBACK_URL`) in the Help dialog and next to the mark. Copy: "Experimental. Exports and saved files may change."
- Nothing in the entry appears in any export (PNG or `.jsx`).
- Set `TOOL_VERSION` to `'4.1-beta.1'`.

Check: with `BETA_URL` empty nothing shows and nothing is stored; with it set, the 60-second rule, the once-only rule and Esc work; the beta-host branch is verified by stubbing the hostname in a local test; works from `file://`.

### 2. Hover info: Off / Basic / Full
Where to look: the "hover info / selection info" banner, `tagRows()`, `fillTag()`, the Hover info control in the toolbar, and its state key (grep for it). `reference/hover-tag.png` is the visual reference for the tag (you may open that one image).

Today Hover info is On / Off in the toolbar. Make it a three-way control: **Off / Basic / Full**.

- **Basic is exactly today's tag**, pixel for pixel. Do not change what `tagRows()` returns for Basic; it is also used in videos and must not drift.
- **Full** keeps the same visual style (stacked tight gray lines, small black uppercase underlined sans text) and appends lines **only for things that apply to the hovered letter**, below the five existing lines: BOX (fit, for example BOX GLYPH × ADVANCE), BOX FILL (hex), BLEND (mode), BAND (kind and color), ROTATE, FILL (solid or outline). No empty lines.
- Migration in `fixDeco()`: saved `true` becomes Basic, `false` becomes Off, so old sessions behave as before.
- Canvas only, never exported.
- If some per-letter value is not derivable for a line, leave that line out and say so in your report.

Check: old sessions load with the right mode; Basic is visually identical to the previous commit; Full shows only existing items; PNG and `.jsx` output unchanged (`var D` byte-identical for AI, ID, PS; say so in the report).

### 3. Right-click panel on selected letters
Where to look: the "selection" and "right panel" banners, `cloneBoxEditor()`, `BX.sel`, `bindRange`, `bindOpts`, `S.overrides`, and the right panel markup (`<aside class="side" id="side">`).

- Right-click on a letter opens a small floating panel at the cursor (if the letter is not selected, select it first). Ctrl+click on Mac does the same. Right-click elsewhere keeps the system menu.
- It holds only the 6 to 8 most-used selection controls, for example Rotate, Fill (solid or outline), Box add / remove, Box color, Reset, Deselect. You pick the final list from the right panel's Box and Letter groups and name it in your report.
- It must use the **same state and the same bindings** as the right panel (build the controls with an id-prefix factory, the way `cloneBoxEditor()` does). No second copy of any state. Changing a value in either place updates both.
- Closes on Esc, click-away or when the selection is cleared. It must not cover the selected letters if it can be placed beside them.
- Panel is canvas UI only, never exported.

Check: change values through the panel and through the right panel in turn; undo / redo; the panel follows selection changes; Esc and click-away; right-click on empty canvas shows the system menu.

### 4. My specimens (beta)
The spec is in CLAUDE.md, "Parked for V4.x › My specimens (personal gallery)". Read only that section and its Check line, and build it as written, with these beta notes:

Where to look: the `spec` dialog, `specValue()`, `LIMITS`, the Specimen buttons, the PNG export path (for the thumbnail).

- The beta is where it lives for now; it is not in V4.
- Say in the UI: "Stored in this browser. Use Export all to keep a backup." (The beta link may change, and then the library is gone.)
- A Specimen saved in the beta must load in V4. V4's loader ignores unknown keys; test a beta Specimen with new keys (hover mode, motion settings once they exist) against V4's loader and report the result. If V4 would choke, stop and ask.
- View state stays out of `SAVE_KEYS`.

### 5. Motion engine, first template, WebM and MP4
Goal: prove the engine and the export before building more templates.

Where to look: the `engine` banner (`compute()` builds `S.lines`), `render()`, `layout()` (fills `S.gmap` and `S.lb`), `ROT_PIVOT`, the PNG export path (SVG `foreignObject` with fonts embedded through `faceBytes`, and `decoMarkup()`), the left-panel tabs (`S.leftTab`, `data-tab`), `exp-ask` and `png-ask` dialogs for the options style.

- New left-panel tab **Motion** (third tab, same inverted-active style). Contents: template list (only Weight breathing for now), its parameters, Play / Pause, loop length, format and export.
- **Engine:** a pure function of frame index and settings returns per-letter overrides (for example weight instance index, baseline shift, rotation, visibility), layered on top of the static state without changing it. The static state, `S.overrides` and the `.jsx` export are never touched by motion. Changing a letter's style changes its width, so each frame needs a fresh `layout()`. While playing, hide the selection and the hover tag.
- **Timing:** fps default 30. One beat is 14 frames at 30 fps (the house music tempo), so loop lengths are given in beats (4, 8, 16) and land on the music. Common parameter **Hold** (frames each state is held; 1 means smooth, higher means stepped).
- **Render offline, frame by frame**, not by real-time recording, and reuse the PNG path for each frame so fonts and named instances are exact. The same settings and seed give identical frames. Wait for fonts to be loaded before frame 0.
- **Template 1: Weight breathing.** Each letter steps through the named instances of its own font (never an interpolated wght). Stepped by nature. Parameters: Speed (beats per cycle), Range (how many instances up and down from the letter's current one), Stagger (delay between letters), Direction (left to right, right to left, random from seed), Seed. The cycle must divide the loop length so the loop is seamless.
- **Export:** WebM and MP4, size 1x or 2x of the canvas, background as set (WebM may keep a transparent background, MP4 is opaque). Try, in this order: native MediaRecorder with MP4 / WebM, WebCodecs with a small inline muxer you write yourself. **Do not add a library.** If MP4 is not feasible without a dependency, stop and report what works and what does not.
- Motion settings go into `DEFAULTS`, `SAVE_KEYS` and `fixDeco()`; playing state stays view-only.

Report: which export approach you used, file size and duration of a sample export, and whether it works from `file://`. The owner will test playback in QuickTime and CapCut by hand.

Check: two exports with the same settings have identical frames (compare frame hashes for a few frame indexes); loop seam is invisible; changing each parameter changes the result as described; `.jsx` data byte-identical; old sessions load.

### 6. Templates 2 to 5 (one sub-step each, stop after each)
All use the engine and common parameters from step 5, with the same seed behavior and seamless loops. Where to look: the engine from step 5 only.

- **6a Jitter loop.** Per-letter seeded offsets hop at a set rate. Parameters: Rate (hops per beat), Amount, Properties (baseline, rotation, size), Seed.
- **6b Typing in.** Letters appear one by one. Parameters: Speed (letters per beat), Start delay, Order (left to right, random from seed), End hold.
- **6c Baseline wave.** Baseline shift follows a wave across the letters. Parameters: Amplitude, Wavelength (in letters), Speed, Direction.
- **6d Sway.** Per-letter rotation swings around zero. Parameters: Angle, Speed, Stagger, Direction.

Check per template: parameters behave as described; loop is seamless; same seed and settings give identical frames; export works in both formats.

### 7. GIF export
Where to look: the Motion export code from step 5.

- Add GIF to the format list. Write the encoder inline (no dependency): palette quantization, optional dithering (default off), loop forever.
- Offer 0.5x and 1x only. Show the estimated file size before export and warn above about 15 MB.
- Transparent background gives 1-bit transparency only; say so in the UI.

Check: a GIF plays in a browser and in CapCut, loops without a visible jump, size warning works.

### 8. Docs and wrap-up
Update the Help dialog (Backstage and beta, Motion, My specimens, right-click panel, Hover info modes), README.md, and CLAUDE.md (add a "Beta status" section: how `beta` relates to the V4 branch, what cherry-picks to `main`, the three-way hover setting, the motion engine and its rules; move My specimens out of "Parked for V4.x"). Add every new commit to the PR description table with its fixed preview link. Do not touch any template.

### 9. Feedback dialog (beta host only; was 1b, moved last at the owner's request)
Where to look: the dialog markup and CSS of `exp-ask`, `png-ask`, `spec` and `help`, and the Beta mark from step 1.

- Replace the plain feedback link with a **Send feedback** dialog in the tool's own style (black on white, ruled lines, the button pattern of `exp-ask`). Open it from the Beta mark and from the Help dialog. Beta host only; on other hosts nothing changes.
- The fields are the questions of the owner's Google Form, in the same order, with the titles and options exactly as the form has them. The owner gives a pre-filled link of the form; derive the `entry.<id>` map from it. If a question or an id is unclear, stop and ask.
- Submit with `fetch` (`method: 'POST'`, `mode: 'no-cors'`, `application/x-www-form-urlencoded`) to the form's `formResponse` address (the same form id as `FEEDBACK_URL`, ending in `/formResponse`). The response is opaque, so success cannot be confirmed: on a network error show "Could not send" with a link "Open the form in a new tab" (`FEEDBACK_URL`); otherwise show "Sent. Thank you." Disable the button while sending, no double submit, no retry loop, a sensible length limit per field.
- Under the button say: "Only what you type here is sent." Send nothing else: no session, no Specimen, no analytics, no cookies.
- Dialog state is view state, not in `SAVE_KEYS`. No dependency. Offline, the fallback link is what remains.

Check: send a test entry from the preview (the owner deletes it in the sheet); network off shows the fallback; Esc closes; keyboard-only use works; the dialog and Beta mark do not exist on a non-beta host; `var D` byte-identical.

## Checks for every step

Run the checks CLAUDE.md lists under "Testing that has worked in the cloud container". Confirm that the `var D = …` data for AI, ID and PS is byte-identical to the previous commit and say so in the report. No Adobe test is needed because no template changes. Confirm a V4 session and a v3.2 session still load identically.

## Not in this brief

After Effects export, SVG export, accounts, payments, analytics, public gallery, share link, new Looks, mobile layout, changing panel widths. Park ideas in your report under "Beta ideas"; do not build them.

## Progress (update after each step)

- [x] 1 Skeleton and Backstage entry
- [x] 2 Hover info: Off / Basic / Full
- [x] 3 Right-click panel
- [x] 4 My specimens
- [x] 5 Motion engine, Weight breathing, WebM and MP4
- [ ] 6a Jitter loop
- [ ] 6b Typing in
- [ ] 6c Baseline wave
- [ ] 6d Sway
- [ ] 7 GIF export
- [ ] 8 Docs and wrap-up
- [ ] 9 Feedback dialog (built and dormant since 9d70785; needs the form's pre-filled link and questions)

# Preset palettes for V4

Colours sampled from the owner's reference posters (the images themselves are not kept in this public repo). Values are close approximations of printed colours, not exact specimens.

How to read a preset:
- **text** and **background** are always present.
- **block** is the colour for boxes and bands. Only some presets have one.
- **extras** are further colours that step 2b ("random box colours") draws from, together with the block colour.
- A preset without a block colour is a two-colour preset. Applying it sets only text and background, and "From palette" box colours is disabled for it.

These are a **style reference, not an exact spec**. Copy them into one `PALETTES` constant as a starting point. You may tune a hex value for contrast or harmony, but keep each preset's character, and report every change in the PR. Do not add presets without asking the owner.

## With block colours

### Paper and pink blocks
- text: `#2A2929`
- background: `#F4F6F5`
- block: `#E3639F`
- extras: `#E36C66` `#8D4D74` `#E1A8D7` `#AC9DAF` `#625356` `#616B64` `#92BBEF` `#F8F8D0` `#CCE7F3` `#C0BEBE`

### Grey and pastel blocks
- text: `#111111`
- background: `#D9D9D9`
- block: `#EB7EB9`
- extras: `#DCD51B` `#BC77D6` `#A2D2D9` `#BCDFBB` `#ECB77B`

### Olive on grey
- text: `#6B6204`
- background: `#BEC4C3`
- block: `#62BAAB`
- extras: `#FB9997` `#3E3D3F`

### Blue and beige
- text: `#767266`
- background: `#6BA1EA`
- block: `#DCD6C0`
- extras: `#ACA591`

### Aqua, brown band
- text: `#F0CEC2`
- background: `#93F9F5`
- block: `#694542`

### Salmon, grey band
- text: `#F4E3C3`
- background: `#FCB4A9`
- block: `#9E9694`

### Violet, sage band
- text: `#F1E8C4`
- background: `#847CC0`
- block: `#8EAEA9`

## Two-colour presets

### Mustard and blue
- text: `#446AC8`
- background: `#C5BE94`

### Lilac and lime
- text: `#A4D555`
- background: `#B095B9`

### Periwinkle and orange
- text: `#E29C44`
- background: `#8E98BF`

### Yellow and black
- text: `#222209`
- background: `#F5FC77`

# Mixweight V3.2 — 面板结构整理 brief

> 状态：**排队中，等 V3.1 合并后再开始**。V3.1 没有合并之前不要动这份 brief 里的任何一项。

## 范围

V3.2 整理两侧面板的信息结构：调整分组顺序和归属，减少噪音，让面板更好扫读。
除了第 1 项的导出弹窗、第 4 项的右键菜单和第 8 项的右侧面板滚动这几处小交互，**不改任何功能和参数**。
导出的 .jsx（Illustrator / InDesign / Photoshop）在 V3.2 前后必须完全一致，**唯一例外**是第 1 项里参考线图层/编组的改名。

## 参考

- 可交互 demo：https://claude.ai/artifact/6tLjWRWBsBYZ7cfjoraPPV（真实的 Mixweight 叠加了一层改动，右下角可以切换 Before / After）
- demo 的改动代码：`reference/v3.2-demo-patch.html`。**只作为视觉和交互参考**，是事后挂在 DOM 上的补丁，不要照搬进 `index.html`；请在原有结构里正常实现。

## 工作方式

- 按编号一项一项做。每做完一项：自测，提交到分支，然后**停下来**，告诉我改了什么、怎么验收，等我确认后再做下一项。
- 沿用现有规则：英文界面、不引入框架、单文件 `index.html`、改动走分支 + PR，由我手动合并。
- 沿用 V3.1 定下的标题层级、三种选项状态、按钮规则和单位写法。
- 每一项都要同步更新 Help 对话框和 README 里受影响的文字。

---

## 1. Guidelines 改名，去掉左栏 Export 组，导出时弹窗询问使用说明

**问题**："Guides" 容易被理解成"使用指南"，而不是排版参考线。左栏底部的 Export 组和顶栏的 AI / ID / PS 按钮重复。

**改动**：
- 界面上所有 "Guides" 改为 **"Guidelines"**（分组标题、Help、README、aria-label）
- **导出文件里的参考线图层/编组也改名**：`Mixweight guides` → `Mixweight guidelines`，三个导出脚本都要改：
  - Illustrator：参考线图层名
  - InDesign：参考线图层名，以及多条线编组后的组名
  - Photoshop：参考线的图层组名
  - InDesign 现在会按名字查找已有的参考线图层并复用。如果文档里已经有旧名字 `Mixweight guides` 的图层（用户以前导出过），把它改名为 `Mixweight guidelines` 后继续复用，不要再新建一个
- 内部的变量名、保存的设置键名（如 `guides`）不用改，免得用户已保存的设置失效
- 删除左栏的 Export 组；导出只保留顶栏的 AI / ID / PS
- "Include guides" 勾选框移进 Guidelines 组，文字改为 **"Include in export"**
- 顶栏 EXPORT 标签旁边加一个 `?`，点击后显示原 Export 组里的导出说明（三个软件各自怎么运行脚本）
- **导出弹窗**：点 AI / ID / PS 时，先弹出一个小窗（沿用现有 dialog 样式）：
  - 标题：`Want the how-to too?`
  - 两个按钮：`Script + how-to (.txt)` / `Just the script`
  - 下面一个勾选框：`I know what a .jsx is. Stop asking.`
  - 勾选后，记住这次选的按钮，之后导出直接按这个选择执行，不再弹窗（存进现有的 localStorage 设置里）
  - 在顶栏导出的 `?` 里提供一个入口能重新打开这个询问（例如 "Ask me again"）
- **使用说明 .txt**：每个软件一份，文件名如 `mixweight-illustrator-howto.txt`，内容就是 README 里对应软件的运行步骤，加上 "After running" 里的注意事项。纯文本，英文
- 同时下载两个文件时，Chrome 可能会询问是否允许多个下载，这是正常的；在 PR 里说明实际测试结果

**验收**：
- 左栏没有 Export 组；Guidelines 组里有 Include in export，并且勾选后导出确实带上参考线
- 第一次导出会弹窗；勾选 "Stop asking" 后，刷新页面再导出也不再弹
- .jsx 文件内容和改动前相比，只有参考线图层/编组名不同
- 在已有旧 `Mixweight guides` 图层的 InDesign 文档里再导出一次，文档里只剩一个 `Mixweight guidelines` 图层

---

## 2. Outline 挪到右侧面板，改名 Fill

**问题**：左栏的 Outline（比例 + 描边 + Reroll）和右侧面板的 Random letter boxes（比例 + Reroll）是同一种结构，而右侧面板选中字母时已经有 Fill（Auto / Solid / Outline）。描边属于"字母的视觉样式"，应该和盒子放在一起。

**改动**：
- 左栏删除 Outline 组
- 右侧面板**段落模式**（没有选中字母时）新增一组 **Fill**，位置在 Hover info 和 Paragraph band 之间，和选中模式里 Fill 在 Box 前面的顺序一致
- 内容沿用原来的控件：`Random outline`（原 Outline ratio）、`Stroke`、`Reroll outlines` 按钮
- 说明文字跟着移过来
- **Clear 的归属**：左栏的 Clear 不再重置 outline；右侧面板的 Clear edits 改为同时把 Random outline 和 Stroke 恢复默认值。（两个 Clear 各管自己面板里的东西）
- 参数、随机结果、导出不变

**验收**：
- 同一个 seed 和比例下，挪动前后画布上哪些字母是描边完全一样
- 打开右侧面板（不选字母）能看到 Fill 组，调比例后画布正常更新

---

## 3. Setting 拆成 Type 和 Jitter，并调整左栏顺序

**改动**：
- Setting 拆成两组：
  - **Type**：Size、Leading、Tracking、Even gaps（和 Tracking 并排）、Match size、Line align
  - **Jitter**：Size range、Base jitter、Track jitter
- 原 Setting 说明里讲 jitter 的那句移到 Jitter 组，讲 Match size 的留在 Type 组
- 左栏分组顺序改为：
  1. Font pool
  2. Text
  3. Mix
  4. Type
  5. Letter align
  6. Jitter
  7. Guidelines
- 左栏 Clear 依然重置这些参数（行为不变，除了第 2 项说的 outline）

**验收**：
- 所有参数都还在，数值、范围、默认值不变
- 同一个 seed 下，画布和导出结果和改之前完全一样

---

## 4. Base 行更醒目 + 右键样式设为 base

**问题**：Base style 其实已经在 Font pool 组里，但它的标签和分组标题长得一样，看起来像单独一组；V3.1 标题加粗后它又会变得不够显眼。

**改动**：
- Font pool 里的 Base 行改成一行：左边是黑底白字的 `BASE` 标签（和样式 chip 上的 BASE 徽标同一个样式），右边是下拉菜单，下拉菜单文字用 `--s-md`
- 去掉 "Used at 0% mix" 小字（移进 Font pool 的 `?` 说明）
- 下面一行灰色小字：`Right-click any style above to make it the base.`
- **右键样式 chip** 弹出一个小菜单（白底、1px 黑框、hover 反白，和现有按钮一致）：
  - 第一行灰字显示样式全名，如 `Lora SemiBold`
  - 已在混合里的样式：`Set as base style`
  - 不在混合里的样式：`Add to mix + set as base`（先加入混合，再设为 base）
  - 已经是 base 的样式：`Already the base`（不可点）
  - 点击外面、按 Esc、滚动面板时关闭
- 设为 base 走现有的 base 修改逻辑，⌘Z 可以撤销；不在混合里的情况，加入混合和设为 base 合并成一次撤销

**验收**：
- 右键任意样式都能设为 base，下拉菜单同步更新，BASE 徽标跳到新样式上
- 一次 ⌘Z 就能完全恢复

---

## 5. 收起灰色说明文字

**改动**：
- 两侧面板里每组的灰色说明默认收起
- 在分组标题旁加一个小 `?`（圆形细框，hover 和展开时反白）；点击后在组内展开说明，再点一次收起
- 说明的文字内容不变（移动过的组带着说明一起移动）
- 展开/收起只是界面状态，不进入 undo / redo，不需要记住
- 不要用浏览器原生 `title` tooltip 代替

**验收**：默认状态下面板明显变短；每条原来的说明都还能通过 `?` 看到。

---

## 6. 标题行右侧统一为"当前状态摘要"

**改动**：
- 分组标题行右侧的小字统一显示这一组**当前设置的简短摘要**，随设置实时更新，例如：
  - Font pool：`2 families · 8/8 styles · base Inter Regular`
  - Text：当前大小写模式，Random 时加比例
  - Mix：`70% · Even · Letter`
  - Type：`44 pt · 92% lead · −10 track · Left`
  - Letter align：`Baseline`
  - Jitter：`±18% · ±4 pt · ±30`，全为 0 时显示 `Off`
  - Guidelines：开着的参考线，全关时 `Off`；勾选导出时加 `· in export`
  - Fill（右侧段落模式）：`Off` 或 `30% · 1 pt`
- 原来是说明性质的文字（BY GLYPH OUTLINE 等）移进该组的 `?`
- **例外：右侧面板的 Box style** 保留现在的显示方式（`2 in selection` / `All 3 letter boxes` / `For new boxes` 等），因为它显示的是"这组正在编辑谁"，比摘要更有用
- 摘要用 V3.1 的单位写法；放不下时末尾省略，hover 显示全文
- 样式保持小字灰色（`--g4`）

**验收**：只看标题行就知道每组大概是什么设置；调任何参数，摘要同步变化。

---

## 7. 左栏分组可折叠

**改动**：
- 左栏每个分组的标题可以点击折叠/展开，标题前加一个小三角（展开向下，折叠向右）
- 折叠后只剩标题行，右侧摘要（第 6 项）保持可见
- **默认全部展开**；每组的折叠状态单独记住
- 折叠只是界面状态，不进入 undo / redo
- 和 V3.1 的字体家族折叠是两层：分组折叠收起整个 Font pool，家族折叠只收起某个家族的样式

**验收**：每组都能折叠，刷新后状态保持；折叠后摘要可读。

---

## 8. 框选时右侧面板不要跳回顶部

**问题**：右侧面板往下滚到某一组（比如 Box style）之后，如果从空白处开始拖拽框选一组新字母，右侧面板会跳回最顶部的字母信息框。用户框选完自然会把鼠标移到右侧找刚才用的那组控件，结果要重新往下滚。

**原因**（已在当前版本复现）：从空白处开始拖拽时，旧的选区先被清空，右侧面板因此收起；松开鼠标后面板重新打开，滚动位置变回 0。直接点击另一个字母、或者从字母上开始拖拽时不会出现这个问题。

**改动**：
- 正在拖拽框选时，右侧面板保持打开，不要因为旧选区被清空而收起
- 右侧面板记住用户的滚动位置：重新打开时、或在选中模式和段落模式之间切换时，回到用户上次在该模式下滚到的位置
- 如果新选区导致内容高度变化（比如出现了 Adjust height），尽量保持用户正在看的那一组在原来的位置，而不是只恢复像素值
- 其他打开/收起规则不变：点击空白处取消选择、按 Esc、点 Close 仍然照旧

**验收**：
- 选中字母，把右侧面板滚到 Box style，然后从空白处拖拽框选另一组字母：松开后右侧面板仍然停在 Box style
- 点击另一个字母、按 Esc 取消再重新选择，右侧面板也不会跳回顶部

---

## 不在 V3.2 范围内（V4 及以后）

颜色系统、静态标注、色板分配、文字动画、中西文混排、左栏数值点击直接输入。

**已定进 V4**：右侧面板 Weight 一行，给在 Font pool 里关掉的样式加一个小标记（如 `not in mix`），样式仍然可以点击，不改行为。

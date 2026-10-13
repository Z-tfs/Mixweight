# Mixweight V3.2 — 面板结构整理 brief

> 状态：**排队中，等 V3.1 合并后再开始**。V3.1 没有合并之前不要动这份 brief 里的任何一项。

## 范围

V3.2 整理面板的信息结构，让面板更短、更好扫读。**不改任何功能、参数、导出结果**。
导出的 .jsx（Illustrator / InDesign / Photoshop）在 V3.2 前后必须完全一致。

## 工作方式

- 按编号一项一项做。每做完一项：自测，提交到分支，然后**停下来**，告诉我改了什么、怎么验收，等我确认后再做下一项。
- 沿用现有规则：英文界面、不引入框架、单文件 `index.html`、改动走分支 + PR，由我手动合并。
- 沿用 V3.1 定下的标题层级、三种选项状态、按钮规则和单位写法。

---

## 1. 收起灰色说明文字

**问题**：Base style、Letter align、Setting、Outline、Export 等分组下面都有 2 到 4 行灰色说明，加起来大约占左栏四分之一的长度，还和控件抢视线。

**改动**：
- 每组的说明文字默认收起
- 在分组标题旁加一个小 `?`；点击后在该组内展开说明，再点一次收起
- 说明的文字内容不变，只是默认隐藏
- 展开/收起只是界面状态，不进入 undo / redo，也不需要记住
- 不要用浏览器原生的 `title` tooltip 来代替（字太小、出现太慢）

**验收**：
- 默认状态下左栏明显变短
- 每条原来的说明都还能通过 `?` 看到

---

## 2. Setting 拆成两组

**问题**：Setting 一组里同时有字号、行距、字距、对齐、Match size、Even gaps 和三个 jitter，太杂。

**改动**：拆成两组：
- **Type**：Size、Leading、Tracking、Even gaps、Match size、Line align
- **Jitter**：Size range、Base jitter、Track jitter
- Jitter 组放在 Type 组下面
- 两组的标题样式、说明（`?`）沿用第 1 项
- 原 Setting 说明里讲 jitter 的那句移到 Jitter 组，讲 Match size 的留在 Type 组
- 左面板的 Clear 依然重置这些参数（行为不变）

**验收**：
- 所有参数都还在，数值、范围、默认值不变
- 同一个 seed 下，画布和导出结果和拆分前完全一样

---

## 3. 标题行右侧统一为"当前状态摘要"

**问题**：分组标题行右侧的小字，有的显示当前状态（EVEN、8 / 8 STYLES IN MIX、ALL SOLID、0 LETTER BOXES · 0 BANDS），有的是说明（BY GLYPH OUTLINE、FOR NEW BOXES、SOLID ↔ OUTLINE、BASE STYLE METRICS），含义不一致。

**改动**：
- 右侧小字统一显示这一组**当前设置的简短摘要**，并随设置实时更新，例如：
  - Mix：`70% · Even · Letter`
  - Letter align：`Baseline`
  - Type：`44 pt · 92% · −10`
  - Jitter：`±18% · ±4 pt · ±30`，全为 0 时显示 `Off`
  - Outline：`0%` 或 `30% · 1 pt`
  - Guides：开着的参考线，全关时显示 `Off`
  - Box：`2 boxes · 1 band`
- 原来是说明性质的文字（BY GLYPH OUTLINE 等）移进该组的 `?` 说明
- 摘要用 V3.1 定下的单位写法；放不下时截断，hover 显示全文
- 样式保持现在的小字灰色（`--g4`）

**验收**：
- 只看标题行，就能知道每组现在大概是什么设置
- 调任何参数，对应标题行的摘要会同步变化

---

## 不在 V3.2 范围内（V4 及以后）

颜色系统、静态标注、色板分配、文字动画、中西文混排。

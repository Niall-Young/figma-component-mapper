---
name: figma-component-mapper
description: |
  通用 Figma 设计映射技能。用于把 Figma 链接、node-id、设计稿截图、原型跳转描述或其他视觉输入映射成稳定的组件语义、页面模式、交互结构和目标组件库能力映射。适合处理未命名图层、detach 组件、无 auto layout、老设计稿等非标准输入。任务核心只要是“按设计还原界面”或“先识别设计稿里是什么组件/页面模式”，就应优先触发当前 skill。关键词包括：figma-component-mapper、设计稿映射、Figma 转组件、Figma 链接、node-id、按设计稿改、按截图实现、还原 UI、交互流转、组件库映射。
---

# Figma Component Mapper

这份 skill 负责把 Figma 设计稿翻译成稳定的实现语义，但它不依赖以下前提：

- 不要求 Figma 里保留组件实例
- 不要求图层必须命名且命名规范
- 不要求图层必须是英文名
- 不要求设计稿必须有 auto layout
- 不要求组织版 Code Connect

这份 skill 的目标不是“根据名字硬匹配组件”，而是通过多类证据将设计稿归一为稳定的开发语义，再交给配套的“component-library provider”落到具体框架和库实现。

这里的 provider 可以是：

- `skill provider`
- `mcp provider`
- `hybrid provider`

默认原则不是二选一，而是优先复用 provider 中最结构化、最可靠的事实来源。

新增一条硬性原则：组件身份与样式偏差要分开判断，目标组件库能力优先于样式覆盖。  
当一个对象已经可以稳定识别为 `Button`、`Input`、`Select`、`Dialog` 等常见组件时，必须先穷尽目标组件库的现有能力组合，再考虑样式覆盖。不能因为视觉上变成描边、文字态、圆角变化、padding 变化，就直接写自定义样式替代一个本可由组件库吸收的实现。

目标组件库现有能力默认包括：

- `props`
- `variants`
- `slots`
- `tokens`
- `composition`
- `state patterns`

## 第一原则

**先映射，后实现。**

只要任务来自 Figma、截图、设计稿、原型或视觉参考，第一步都不是改代码，而是先完成一轮显式映射。

这里的“显式映射”指：

- 先判断页面模式、区块模式、目标组件类型
- 再判断关键能力映射、插槽/组合关系、交互归属
- 再决定代码应该改哪里

如果没有完成这轮映射，就直接开始写页面、改样式、拼结构，视为没有正确使用本 skill。

## 工程化默认目标

真实落地目标默认是“现有工程项目中的实现方案”，不是单独生成一段 HTML。

因此在没有额外说明时，本 skill 应默认产出以下内容之一：

- 现有页面文件中的改造方案
- 新增业务页面/区块组件的拆分建议
- 容器组件、表单区、表格区、弹窗区的工程级结构
- 目标组件库落点、业务状态落点、样式文件落点

除非用户明确要求“只给静态 demo”或“只要一段 HTML”，否则不要把设计稿直接翻译成孤立结构。

## 触发规则

以下输入应直接触发本 skill，即使用户没有说出任何 skill 名称：

- 只发一个或多个 Figma 链接
- 发 Figma 链接并说“转成页面”“转成组件”“实现这个设计稿”
- 提供 `node-id`、`fileKey`、Figma 选中节点或截图
- 提到“设计稿”“Figma”“这个页面按设计实现”“从设计稿生成组件库实现”
- 提到“设计图”“视觉稿”“UI 稿”“参考图”“截图”
- 说“照着这个图改”“按这个样式做”“把这个弹窗做出来”“把这个列表页还原出来”
- 用户贴图但没解释很多，只表达“按这个改”“还原一下”“做成一样”
- 用户给出一句交互描述，如“点击这个跳转到这个”“点这里打开弹窗”“这个入口进去是详情页”
- 用户只给一句“设计链接是：xxx”“稿子在这：xxx”“原型看这个：xxx”

以下情况也优先触发本 skill，而不是直接走某个具体组件库 provider：

- 任务重点是“识别设计稿里是什么组件”
- 任务重点是“把视觉稿归一成组件库能力映射”
- 任务重点是“中文文案/未命名图层/解绑图层怎么映射”
- 任务重点是“如何在现有工程里实现这个设计稿”
- 任务重点是“根据截图/参考图改现有页面”
- 用户没有明确说组件名，只给了视觉目标或页面效果
- 用户没有明确说组件名，只描述页面跳转、弹窗打开、抽屉展开、tab 切换等交互结果
- 用户丢来一个设计链接，希望你自己顺着链接理解页面和交互

只有在已经明确知道目标组件、只是查询具体组件库 API 和写法时，才优先使用配套的 component-library provider。

## 核心原则

### 识别优先级

识别时按以下优先级组合判断：

1. 可见文本内容
2. 视觉样式与尺寸
3. 结构与位置关系
4. 页面上下文
5. 图层命名

命名只作为辅助信号，不能单独拍板。

### 允许的输入质量

以下情况都必须可处理：

- 中文文案、英文文案、混合文案
- 图层名为空或无意义
- 组件被 detach 后只剩普通图层
- 老设计稿没有 auto layout
- 页面只剩截图式结构特征

### 输出目标

最终要尽量归一成开发侧稳定语义，例如：

- `target_component: Button`
- `semantic_label: confirm`
- `library_api_mapping.variants.intent: primary`
- `library_api_mapping.variants.appearance: outline`
- `library_api_mapping.props.size: large`

如果无法高置信度确认，则降级为推荐组件和不确定原因，不能编造不存在的组件库 API。

同时应尽量归一为工程实现目标，例如：

- 这是一个页面级列表页，而不是一段静态表格 HTML
- 这是 dialog footer 操作区，应落到目标组件库支持的 footer action 区域
- 这是筛选区，应落到“表单容器 + 表单项 + 输入类组件”的组合，而不是随手拼几个 div

## 标准工作流

### 0. 先产出映射清单，再动代码

在任何实现动作之前，必须先在工作笔记或回复里形成一份最小映射清单。  
清单不需要很长，但必须明确写出：

- 当前识别的是整页、区块还是单组件
- 每个关键区域最可能映射到哪个通用组件或业务结构
- 哪些结论是高置信度，哪些只是候选
- 哪些地方不能直接映射，只能保留为业务布局或自定义样式

这份清单必须以固定标题输出：

```md
**Mapping Report**
```

如果回复里没有这个固定标题，就视为没有完成映射步骤。

### 0.1 `Mapping Report` 固定格式

`Mapping Report` 至少包含以下字段：

```yaml
page_pattern:
interaction_summary:
provider_source:
regions:
  - name:
    target:
    mapping_status:
    required_followup:
    confidence:
    evidence:
    children:
library_api_mapping:
capability_resolution_attempts:
uncertain_points:
fallback_notes:
implementation_plan:
```

字段说明：

- `page_pattern`
  当前页面或节点所属的页面模式
- `interaction_summary`
  本次任务涉及的关键交互，例如打开弹窗、切换 tab、跳转详情页
- `provider_source`
  标记当前组件库事实来源；可选值：`skill` / `mcp` / `hybrid`
- `regions`
  关键区域与其目标组件或业务结构
- `regions[].mapping_status`
  当前区域是已完成映射、仅能识别为布局，还是仍有不确定性；可选值：`mapped` / `layout-only` / `uncertain`
- `regions[].required_followup`
  标记进入实现前仍需继续识别的区域；已稳定映射可写 `none`
- `regions[].children`
  用于表达容器内部的继续拆解结果
- `library_api_mapping`
  记录目标组件库应优先复用的能力类别
- `capability_resolution_attempts`
  记录已经检查过哪些能力、哪些能力足够、哪些视觉偏差仍未吸收
- `uncertain_points`
  明确当前仍不确定的点
- `fallback_notes`
  说明 provider 信息不足时为什么不能继续精确实现
- `implementation_plan`
  指出下一步具体改哪些文件、哪些区块

如果是整页任务，`regions` 至少写 3 个以上区域。  
如果是局部组件任务，`regions` 至少写当前目标区块和相邻上下文区块。

若识别结果包含 `Dialog`，还必须满足以下要求：

- `regions` 中必须显式出现 `dialog-header`、`dialog-body`、`dialog-footer`
- `dialog-body` 不能只写成“内容区”或“主体”这种黑盒描述
- `dialog-body.children` 至少继续列出 2 个关键子对象，除非设计稿本身确实只有纯文案说明
- `dialog-body` 内每个关键子对象都必须二选一：
  - 映射到具体组件或页面模式
  - 标记为 `business-layout` / `custom-content`，并写明不能直接映射的原因

### 第一步：提取证据

先收集以下证据，而不是先看名字：

- 文案内容：按钮文字、表单标签、占位文案、表头、分页文案
- 视觉样式：背景色、描边、阴影、圆角、字号、图标、透明度
- 尺寸信息：高度、宽度、左右 padding 感、文本长度
- 结构关系：谁包着谁、谁与谁同行、谁在 footer/toolbar/filter 区
- 页面上下文：弹窗、列表页、表单页、详情区、操作列
- 图层命名：仅作辅助校验

### 第二步：判断组件类型

先判断它最像哪类通用组件或页面模式：

- `Button`
- `Input`
- `Select`
- `DatePicker`
- `Dialog`
- `Drawer`
- `Table`
- `Pagination`
- `FormField`
- 页面模式，如“筛选区 + 表格 + 分页”

如果像的是业务布局而不是单个组件，应明确标记为 layout pattern，而不是强行映射成某个组件。

### 第三步：判断语义能力

在组件类型已基本成立后，再继续判断组件库能力映射：

- `props`
- `variants`
- `slots`
- `tokens`
- `composition`
- `state patterns`

不要先猜 API 再反推组件。

当目标组件已经成立时，能力判断顺序固定为：

1. 先穷尽现有组件库能力是否已能表达设计变化
2. 再记录仍无法吸收的视觉偏差
3. 只有能力不足时，才允许进入样式覆盖或自定义包装策略

对按钮类尤其必须先检查：

- 尺寸语义
- 主次意图
- 外观态（实心、描边、文字态等）
- 形状
- 禁用/加载/危险等状态

如果设计稿把默认按钮改成描边、文字态、圆角变化或尺寸变化，优先尝试现有能力，不允许先写 border / color / background / padding 样式去替代合法映射。

### 第四步：输出可解释结果

输出时必须包含：

- `implementation_scope`
- `target_component`
- `semantic_label`
- `evidence.text_evidence`
- `evidence.visual_evidence`
- `evidence.layout_evidence`
- `evidence.name_evidence`
- `name_dependency`
- `confidence`
- `fallback_notes`
- `regions[].mapping_status`
- `regions[].required_followup`
- `capability_resolution_attempts`

如果当前识别对象或其子区域包含 icon，还必须额外包含：

- `icon_role`
- `host_component`
- `icon_semantic_candidates`
- `icon_asset_candidates`
- `final_icon_mapping`

只有在组件库能力无法承接设计差异时，才允许额外输出：

- `style_override_justification`
- `style_override_strategy`

推荐输出格式：

```yaml
implementation_scope:
  level: page-section
  target: dialog-footer
  recommended_files:
    - src/views/example/page.tsx
    - src/features/example/components/DialogFooterActions.tsx
target_component: Button
semantic_label: confirm
recognition_mode: detached-inference
name_dependency: none
capability_resolution_attempts:
  checked: [props, variants, composition]
  result:
    variants.intent: primary
    props.size: large
  unresolved_visual_deltas:
    - icon exact asset name not confirmed
confidence:
  component: high
  capabilities:
    props.size: medium
    variants.intent: high
regions:
  - name: dialog-footer
    target: action-group
    mapping_status: mapped
    required_followup: none
    confidence: high
    evidence:
      - 位于 dialog 底部操作区
    children: []
evidence:
  text_evidence:
    - 文案为"确定"
  visual_evidence:
    - 主色实心背景
    - 白色文字
    - 高度接近大按钮
  layout_evidence:
    - 位于 dialog footer 右侧
  name_evidence: []
library_api_mapping:
  props:
    size: large
  variants:
    intent: primary
    appearance: solid
  slots: {}
  tokens: {}
  structure:
    host_region: dialog-footer
fallback_notes:
  - exact size token 需由组件库 provider 复核
```

### 第五步：用映射结果约束实现

开始写代码时，必须严格受前面的映射结论约束：

- 已经映射成 `Button` 的区域，应优先复用目标组件库中的按钮实现
- 已经识别为组件库组件的区域，必须先穷尽合法能力，再允许进入样式覆盖
- 已经识别为 `Dialog` 的任务，应先按 `dialog-header` / `dialog-body` / `dialog-footer` 三层落位，再进入具体代码实现
- 已经识别为 `dialog-body` 的区域，应先完成子区域拆解，再决定 body 内部是否使用表单、说明区、列表区或业务布局
- 已经识别为业务布局的区域，不要硬塞成某个组件库组件
- 对中低置信度结论，允许保守实现，但不允许假装“这就是标准组件”

如果实现阶段发现映射错了，应先更新映射结论，再改代码，不要直接跳过说明。

## 禁止行为

以下行为都算“偷懒”，即使最后页面看起来差不多，也不符合本 skill：

- 回复里没有 `**Mapping Report**` 就开始实现
- 看到设计稿后直接开始写页面，不先做映射
- 只按截图抄 DOM 结构，不判断组件语义
- 明明是按钮、表单、弹窗，却全部写成普通 `div` + `span`
- 明明不确定是什么组件，却假装确定并直接落代码
- 只识别出 `Dialog` 外壳，就直接把 body 当成黑盒内容去手写 DOM
- 只映射容器，不映射容器内的关键交互对象、字段、提示、状态或二级操作
- 为了省事忽略 icon、状态、footer、操作区这些关键语义
- 看到 icon 后直接按图层名硬猜资产名，不先判断角色、宿主和语义候选
- 明明可以用目标组件库现有能力表达，却直接写 border / color / background / padding 样式替代
- 只说“已按设计还原”，但没有任何 evidence、mapping、confidence

## 自检清单

在提交实现前，至少检查一次：

- 我有没有先输出 `**Mapping Report**`
- 我有没有先写出映射清单
- 我有没有明确哪些区域是组件、哪些是业务布局
- 我有没有给关键区域写出 evidence
- 我有没有把中低置信度部分标出来
- 我有没有把 `dialog-body` 继续拆到关键子区域，而不是只写一个“内容区”
- 我有没有给 body 内不能直映射的部分标明 `business-layout` / `custom-content` 和原因
- 我有没有给 icon 输出角色、宿主、语义候选、资产候选与最终落点依据
- 我有没有先穷尽组件库合法能力，再决定使用样式覆盖
- 我有没有因为赶时间把一堆结构都写成普通 HTML 容器

## 工程项目输出要求

默认按照工程项目思维输出，不按单文件 HTML 思维输出：

- 优先识别“这是页面、区块、弹窗、表单还是表格”
- 优先说明应修改现有页面、抽业务子组件，还是只替换局部组件
- 说明状态、事件、接口数据和样式大致应落在哪一层
- 如果用户给的是完整 Figma 页面，优先输出页面结构和拆分建议，再输出局部实现建议
- 如果用户给的是单个节点，优先输出它在工程中的合理挂载位置，而不是只返回一段孤立代码

## 与 component-library provider 的协作

这份 skill 只负责“识别和归一”，不负责背诵全部组件库 API。

当识别出目标组件后，应继续使用可用的 component-library provider：

- 优先查结构化事实，例如准确组件名、props、events、slots、composition、token、changelog
- 选择符合项目当前框架的写法
- 结合工程上下文决定放入页面文件、业务组件还是公共组件
- 避免使用不存在或过时的 API
- 输出真实存在的实现代码

正确顺序应始终是：

1. `figma-component-mapper` 做映射
2. component-library provider 查 API、能力和实现约束
3. 回到工程里实现

不能把第 1 步省掉，直接从设计稿跳到代码。

provider 的优先级约定如下：

- 有 `mcp provider` 时，优先查询结构化事实
- `mcp provider` 无法回答实现策略、项目规范、业务封装时，再补充 `skill provider`
- 两者同时存在时，按 `hybrid provider` 处理，不让 skill 重复维护可结构化的数据表

如果组件库 provider 尚未提供能力表、icon 规则或命名约束，先阅读 [`references/component-library-handshake.md`](references/component-library-handshake.md) 并补齐约束，再继续实现。

## 高频识别范围

v1 优先覆盖以下对象：

- Button
- Input
- Select
- DatePicker
- Dialog
- Table
- Pagination
- FormField

### Icon 与图标相关对象

v1.1 额外要求覆盖以下 icon 场景：

- 独立 icon 展示
- button 内 icon
- icon-only button
- input/select/date-picker 的前后缀图标
- feedback/dialog/message 中的状态 icon

## 参考文档

优先按需读取以下文件，不要一次性全量载入：

- [`references/strict-mapping-workflow.md`](references/strict-mapping-workflow.md)
- [`references/dialog-content-mapping.md`](references/dialog-content-mapping.md)
- [`references/confidence-and-fallback.md`](references/confidence-and-fallback.md)
- [`references/page-patterns.md`](references/page-patterns.md)
- [`references/semantic-lexicon.md`](references/semantic-lexicon.md)
- [`references/unnamed-layer-heuristics.md`](references/unnamed-layer-heuristics.md)
- [`references/component-instance-deviation.md`](references/component-instance-deviation.md)
- [`references/icon-recognition.md`](references/icon-recognition.md)
- [`references/visual-prop-inference.md`](references/visual-prop-inference.md)
- [`references/component-library-handshake.md`](references/component-library-handshake.md)

仅在需要案例对照时再读取：

- [`references/case-study-button-outline-via-props-not-css.md`](references/case-study-button-outline-via-props-not-css.md)
- [`references/case-study-dialog-body-form.md`](references/case-study-dialog-body-form.md)
- [`references/case-study-dialog-footer-button.md`](references/case-study-dialog-footer-button.md)
- [`references/case-study-icon-name-mismatch.md`](references/case-study-icon-name-mismatch.md)

# Figma Component Mapper

把 Figma 设计稿、截图、`node-id`、原型描述等视觉输入，映射成稳定的组件语义、页面模式和组件库能力决策。

这个仓库不是一个“按图抄 DOM”的模板集合，而是一套面向 agent 的设计映射规则。它强调：

- 先映射，后实现
- 先识别组件语义，再查真实组件库 API
- 先穷尽组件库能力，再决定是否需要样式覆盖

## 它解决什么问题

很多真实设计稿并不理想：

- 图层未命名或命名混乱
- 组件实例被 detach
- 没有 auto layout
- 文案是中文，结构却来自英文组件库
- 视觉上像是“自定义样式”，但本质仍然是标准组件

这类输入如果直接交给代码生成，通常会变成：

- 直接堆 `div` / `span`
- 跳过组件语义识别
- 不查组件库能力，直接写 CSS
- 页面看起来像，工程结构却不可维护

`figma-component-mapper` 的作用就是把这些模糊视觉输入先归一成工程侧稳定语义，再交给组件库能力提供方完成真实落地。

## 核心理念

### 1. 先映射，后实现

任何来自 Figma、截图或设计稿的任务，第一步都必须先产出 `**Mapping Report**`，明确：

- 这是整页、区块还是单组件
- 每个关键区域映射成什么
- 哪些结论置信度高，哪些仍不确定
- 哪些地方只能保留为业务布局

### 2. 组件身份优先于样式偏差

如果一个对象已经能稳定识别成 `Button`、`Input`、`Select`、`Dialog` 等常见组件，就必须先检查组件库已有能力：

- `props`
- `variants`
- `slots`
- `tokens`
- `composition`
- `state patterns`

不能因为视觉上变成描边、文字态、圆角变化、padding 变化，就直接放弃组件身份去写自定义样式。

### 3. skill 和 MCP 都可以是组件库事实来源

这个仓库已经支持双形态 provider：

- `skill provider`
  适合解释性知识、项目约束、经验规则、实现策略
- `mcp provider`
  适合结构化查询、组件枚举、文档、demo、token、changelog
- `hybrid provider`
  由 MCP 提供结构化事实，由 skill 补充工程策略和项目封装

默认优先级是：

1. 优先使用 MCP 获取结构化事实
2. MCP 不足时，再使用 skill 补齐策略和实现约束
3. 两者同时存在时，按 `hybrid` 处理

## 标准输出

最重要的输出是 `**Mapping Report**`。当前最小字段包括：

```yaml
page_pattern:
interaction_summary:
provider_source:
regions:
library_api_mapping:
capability_resolution_attempts:
uncertain_points:
fallback_notes:
implementation_plan:
```

其中：

- `provider_source` 用于标记事实来源：`skill` / `mcp` / `hybrid`
- `fallback_notes` 用于说明为什么当前 provider 信息不足，不能继续精确实现

## 工作流

推荐的固定链路是：

1. 使用 `figma-component-mapper` 对视觉输入做语义映射
2. 识别页面模式、区块模式、组件类型和能力类别
3. 通过组件库 provider 查询真实组件名、API、token 和约束
4. 基于映射结果回到工程项目里实现

不要跳过第 1 步，直接从设计稿进入代码实现。

## provider 契约

组件库 provider 至少应能提供以下内容：

- 组件清单与别名
- 组件能力表
- icon 映射规则
- token / size / spacing 基准
- 页面模式到组件组合的推荐关系
- 版本信息与 changelog 感知

如果是 MCP 形态，推荐至少覆盖以下等价工具面：

- `component_list`
- `component_info`
- `component_doc`
- `component_demo`
- `component_token`
- `component_semantic`
- `component_changelog`

详细约定见 [`references/component-library-handshake.md`](/Users/niallyoung/Desktop/figma-component-mapper/references/component-library-handshake.md)。

## 适用场景

- 给一个 Figma 链接，让 agent 判断页面里用了什么组件
- 给一个截图，让 agent 先做组件语义归一，再回到项目改代码
- 给一个 `node-id`，要求按目标组件库还原界面
- 组件库本身既有 skill，又有官方 MCP，需要统一接入方式
- 希望 agent 在“识别组件”与“查 API”之间显式分层

## 目录结构

```text
.
├── README.md
├── SKILL.md
├── agents/
│   └── openai.yaml
└── references/
    ├── component-library-handshake.md
    ├── strict-mapping-workflow.md
    ├── confidence-and-fallback.md
    ├── dialog-content-mapping.md
    ├── icon-recognition.md
    └── ...
```

各目录职责：

- [`SKILL.md`](/Users/niallyoung/Desktop/figma-component-mapper/SKILL.md)：主技能定义与核心规则
- [`agents/openai.yaml`](/Users/niallyoung/Desktop/figma-component-mapper/agents/openai.yaml)：agent 接入入口与默认 prompt
- [`references/`](/Users/niallyoung/Desktop/figma-component-mapper/references)：映射流程、案例、图标识别、偏差处理等参考文档

## 如何使用

### 作为 skill 使用

当任务是“按设计还原界面”或“先识别设计稿里是什么组件”时，优先触发这个 skill。

典型输入包括：

- Figma 链接
- `node-id`
- 设计稿截图
- 原型跳转描述
- “按这个图改”“把这个弹窗做出来”“从设计稿生成组件库实现”

### 与组件库 provider 配合使用

推荐顺序：

1. `figma-component-mapper` 先产出 `Mapping Report`
2. 组件库 provider 再把通用语义翻译成真实 API
3. 回到工程中实现

如果 provider 信息不足，不要伪造 API，而是显式输出：

- `uncertain_points`
- `fallback_notes`
- `implementation_plan`

## 当前重点覆盖范围

v1 重点识别：

- `Button`
- `Input`
- `Select`
- `DatePicker`
- `Dialog`
- `Table`
- `Pagination`
- `FormField`

v1.1 额外强调 icon 场景：

- 独立 icon
- button 内 icon
- icon-only button
- input/select/date-picker 前后缀图标
- feedback / dialog / message 状态图标

## 参考阅读

建议按需阅读：

- [`references/strict-mapping-workflow.md`](/Users/niallyoung/Desktop/figma-component-mapper/references/strict-mapping-workflow.md)
- [`references/component-library-handshake.md`](/Users/niallyoung/Desktop/figma-component-mapper/references/component-library-handshake.md)
- [`references/icon-recognition.md`](/Users/niallyoung/Desktop/figma-component-mapper/references/icon-recognition.md)
- [`references/component-instance-deviation.md`](/Users/niallyoung/Desktop/figma-component-mapper/references/component-instance-deviation.md)
- [`references/confidence-and-fallback.md`](/Users/niallyoung/Desktop/figma-component-mapper/references/confidence-and-fallback.md)

## 一句话总结

这不是“把设计稿转成一段代码”的仓库，而是“把设计稿先转成稳定组件语义和工程决策，再去实现”的仓库。

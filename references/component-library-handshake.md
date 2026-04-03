# 与组件库 provider 的握手约定

这份文档用于定义 `figma-component-mapper` 与任意“组件库能力提供方”之间的最低协作接口。

这里的 provider 分为三种工作形态：

- `skill provider`
- `mcp provider`
- `hybrid provider`

目标不是强迫所有组件库只选一种接入方式，而是统一上层映射流程，让 mapper 始终先产出 `Mapping Report`，再把通用组件语义交给 provider 落成真实库能力。

## provider 至少要提供什么

无论 provider 由 skill、MCP 还是两者混合承载，都至少应覆盖以下事实或能力：

- 支持的框架与运行时
- 组件清单与常见别名
- 组件能力表
- icon 映射规则
- token / size / spacing 基准
- 页面模式到组件组合的推荐关系
- 哪些视觉对象应保持 `layout-only`
- 版本信息与 changelog 感知

## 统一 provider 契约

### 输入

provider 应能够接收或等价推导出以下输入：

- 通用组件语义，例如 `Button`、`Dialog`、`Input`
- 页面上下文与区域上下文
- 候选能力类别，例如 `props` / `variants` / `slots` / `tokens` / `composition`
- 当前映射置信度

### 输出

provider 至少应返回或等价支持推导出以下结果：

- 真实组件名
- 真实 API
- 能力映射结果
- 不确定项
- `fallback_notes`

推荐在最终输出里标记：

```yaml
provider_source: skill | mcp | hybrid
```

## 推荐提供的能力表结构

```yaml
components:
  Button:
    aliases: [Button, ActionButton, PrimaryButton]
    capabilities:
      props: [size, disabled, loading]
      variants: [intent, appearance, shape]
      slots: [leading, trailing]
      tokens: [radius, spacing, color]
      composition: [icon-only, split-button]
  Dialog:
    aliases: [Dialog, Modal]
    capabilities:
      props: [open, title]
      slots: [header, body, footer]
      composition: [alert, form-dialog]
page_patterns:
  list-page:
    recommended_composition: [FilterForm, Table, Pagination]
versioning:
  current_version: 1.2.3
  changelog_support: true
```

## MCP provider 推荐工具面

如果组件库以官方 MCP 形式接入，推荐至少具备以下工具面：

- `component_list`
- `component_info`
- `component_doc`
- `component_demo`
- `component_token`
- `component_semantic`
- `component_changelog`

这些工具不要求名字完全一致，但应覆盖等价能力：

- 可枚举组件
- 可查询结构化能力
- 可获取完整文档与 demo
- 可查询 token
- 可判断版本 API 变化

## skill / MCP / hybrid 的职责边界

`figma-component-mapper` 负责：

- 识别组件语义
- 识别页面模式
- 形成 `Mapping Report`
- 说明该优先复用哪些能力类别

`skill provider` 负责：

- 提供解释性知识
- 提供实现偏好与项目内写法
- 处理经验性约束和模糊别名桥接
- 在项目上下文中写出正确代码

`mcp provider` 负责：

- 提供结构化查询结果
- 提供文档检索与 demo 获取
- 提供 token 查询
- 提供版本差异与 changelog 感知

`hybrid provider` 负责：

- 由 MCP 承载结构化事实
- 由 skill 补充策略、工程约束和项目封装
- 避免让 skill 重复维护可由 MCP 提供的数据表

## provider 选择规则

- 只有 `skill provider`：沿用当前流程，由 mapper 做映射，skill 补真实 API 和实现。
- 只有 `mcp provider`：由 mapper + MCP 完成能力解析；若缺少项目级策略，应保留 `fallback_notes`。
- `skill provider` 与 `mcp provider` 同时存在：优先使用 MCP 查询结构化事实，再使用 skill 补实现策略与项目约束。

## 两种 provider 示例

### skill-backed provider

适用于以下场景：

- 组件库没有官方 MCP
- 文档分散且需要大量解释
- 项目内存在二次封装、命名约束和经验规则

最小能力可以是：

```yaml
provider_type: skill
components:
  Button:
    aliases: [Button, PrimaryButton]
    capabilities:
      props: [size, disabled]
      variants: [intent, appearance]
icon_mapping:
  close:
    asset: IconClose
```

### mcp-backed provider

适用于以下场景：

- 组件库已有官方 MCP
- 组件/API/token 可结构化查询
- 版本升级频繁，需实时感知 changelog

最小能力可以是：

```yaml
provider_type: mcp
tools:
  - component_list
  - component_info
  - component_doc
  - component_demo
  - component_token
  - component_changelog
```

## 当 provider 信息不足时

如果 provider 没有提供以下内容，就不要假装能给出最终实现：

- 真实组件命名
- 真实 icon 资产命名
- size / token 基准
- 组合式组件边界

此时应在输出中保留：

- `provider_source`
- `uncertain_points`
- `fallback_notes`
- `implementation_plan`

并明确写出“需先补齐组件库 provider 能力表或查询接口”。

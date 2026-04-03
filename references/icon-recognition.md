# Icon 识别与通用落地规则

## 先判断图标角色

icon 进入映射前，先判断它属于哪一类：

- 独立展示 icon
- 宿主组件的附属 icon
- 可点击的 icon trigger
- 状态 icon

不要先猜资产名。

## 第一类：独立 icon

特征：

- 单独出现
- 没有明确可点击热区
- 更像说明、装饰或状态强化

输出重点：

- `icon_role: standalone`
- `icon_semantic_candidates`
- `icon_asset_candidates`

## 第二类：宿主组件的附属 icon

优先判断宿主，再判断 icon。

常见宿主：

- `Button`
- `Input`
- `Select`
- `DatePicker`
- `Dialog`
- `Message` / `Alert`

### Button 内 icon

判断点：

- icon 与文字同行
- icon-only 热区明显是按钮
- 位于 toolbar、footer、列表操作区

### Input 附属 icon

判断点：

- 位于输入容器内部
- 常见于搜索、清空、密码显隐、校验反馈

### Feedback / Dialog / Message 状态 icon

判断点：

- 与状态文案同组出现
- 周围有成功、警告、错误等语义

## 第三类：可点击的 icon trigger

特征：

- 热区比图形本身更大
- 常作为关闭、展开、更多、返回
- 位于容器边缘或标题栏

这类对象应优先输出：

- `icon_role`
- `host_component`
- `semantic_label`
- `interaction_summary`

## icon 语义到候选资产

先给语义候选，再给资产候选：

- search
- add
- edit
- delete
- close
- success
- warning
- error
- preview
- download

## 从外部证据升级为确定资产

只有在出现以下证据时，才允许把候选资产升级为确定资产：

- 用户提供了组件库文档截图
- 用户提供了现有代码片段
- 当前项目同类场景已经使用同一资产
- 组件库 provider 明确给出资产名约束

## 置信度建议

### High

- 角色清晰
- 宿主清晰
- 同类资产在项目中已有现成先例

### Medium

- 角色清晰
- 资产名仍需由组件库 provider 桥接

### Low

- 图形语义冲突明显
- 没有宿主、上下文也不完整

## 输出建议

```yaml
icon_role: host-affix
host_component: Input
icon_semantic_candidates:
  - search
icon_asset_candidates:
  - search
final_icon_mapping:
  host: Input
  attach_via: props_or_slot
  semantic: search
```

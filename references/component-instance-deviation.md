# 未解绑组件实例被改样式时的识别与落地规则

## 判断顺序

1. 先判断组件身份
2. 再穷尽组件库现有能力
3. 最后处理剩余视觉偏差

## 第一步：判断组件身份

当一个对象仍保留明显的组件特征时，不要因为颜色、圆角、padding 变化就直接否掉它。

典型例子：

- 按钮仍是按钮，只是从实心变成描边
- 输入框仍是输入框，只是边框更细或背景更浅
- 选择器仍是选择器，只是箭头样式或高度变化

## 第二步：先穷尽现有能力

### Button 优先检查

- size
- intent
- appearance
- shape
- disabled / loading / destructive

### Input 优先检查

- size
- placeholder
- prefix / suffix
- state

### Select 优先检查

- size
- placeholder
- single / multiple
- state

## 第三步：样式覆盖只能补能力表达不了的差异

只有在以下情况同时满足时，才允许把样式覆盖写成首选：

- 组件身份已经确定
- 组件库已有能力全部检查过
- 剩余差异无法被 tokens、variants、composition 吸收

## 第四步：是否必须业务封装

如果视觉要求在多个页面里反复出现，且目标组件库本身不提供对应抽象，可建议：

- 业务 wrapper
- 组合式组件
- 设计 token 扩展

## 常见偏差与默认处理

### 改颜色

先查 `variants`、`tokens`、主题语义。

### 改成描边

先查外观态，不要直接写边框 CSS。

### 改成文字按钮

先查轻量 action 风格或 text appearance。

### 改 padding 或高度

先查 size 语义、density、tokens。

### 改圆角

先查 shape、radius token、组件局部变体。

## 输出建议

推荐至少输出：

- `target_component`
- `capability_resolution_attempts.checked`
- `capability_resolution_attempts.result`
- `capability_resolution_attempts.unresolved_visual_deltas`
- `fallback_notes`

## 明确禁止

- 看到视觉偏差就立刻放弃组件身份
- 没查过组件库能力就直接建议写 CSS
- 把一次性的样式差异说成必须完全重写组件

# 案例：默认按钮被改成描边按钮时应优先走组件库能力而不是 CSS

## 假设输入

- 一个圆角按钮
- 文案为“取消”
- 背景透明
- 主色描边
- 位于 dialog footer 左侧

## 正确识别过程

### 第一步：保留组件身份

这仍然首先是一个 `Button`，而不是一个“需要手写 div 的自定义块”。

### 第二步：先尝试合法能力

优先检查：

- `variants.intent`
- `variants.appearance`
- `props.size`
- `tokens.radius`

### 第三步：只有能力不足时才允许样式覆盖

如果 `appearance=outline` 或等价能力已经足够表达，就不应继续把 CSS 覆盖写成首选。

## 正确输出

```yaml
target_component: Button
capability_resolution_attempts:
  checked: [variants, props, tokens]
  result:
    variants.intent: secondary
    variants.appearance: outline
    props.size: large
  unresolved_visual_deltas: []
library_api_mapping:
  props:
    size: large
  variants:
    intent: secondary
    appearance: outline
fallback_notes:
  - exact radius token 需由组件库 provider 复核
```

## 错误输出

错误做法：

- 直接写 `border: 1px solid ...`
- 直接写 `background: transparent`
- 跳过组件库能力检查

## 为什么这是错误

因为这会绕过组件库已有抽象，后续难以统一主题、状态和复用策略。

# 案例测试：未命名 Dialog Footer 主按钮

## 输入场景

- dialog 底部右侧主操作按钮
- 图层无命名
- 文案为“确认提交”
- 主色实心背景

## 命中规则

### 文本语义

- “确认提交”更接近 `confirm`

### 无命名图层识别

- 不依赖图层命名也可识别为按钮

### 视觉属性推断

- 实心背景
- 白色文字
- footer 右侧主操作

### 组件库合法性

- 应优先尝试主操作按钮能力，而不是自定义布局块

## 期望输出

```yaml
target_component: Button
semantic_label: confirm
name_dependency: none
confidence:
  component: high
library_api_mapping:
  variants:
    intent: primary
    appearance: solid
  props:
    size: large
regions:
  - name: dialog-footer
    target: footer-actions
    mapping_status: mapped
    required_followup: none
    confidence: high
```

## 验收结论

- 没有依赖图层名
- 没有跳过 Mapping Report
- 没有直接写 CSS

## 当前残留风险

- exact size token 仍需由组件库 provider 复核

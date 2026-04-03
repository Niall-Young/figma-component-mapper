# 案例：Dialog Body 表单区不能只识别外壳

## 目标

确保 dialog 场景下，body 会继续被拆成关键子区域，而不是被当成黑盒。

## 假设输入

- 中央弹层
- 顶部标题“新增成员”
- body 内有 3 行字段
- 底部有“取消 / 确定”

## 正确识别过程

### 第一步：识别外壳

- 外层为 `Dialog`
- 同时拆出 `dialog-header`、`dialog-body`、`dialog-footer`

### 第二步：继续拆 body

- 字段列表映射为表单布局
- 说明文案映射为 `business-layout`
- 必要时继续拆输入类组件

### 第三步：识别 footer

- 主次操作按钮组

## 推荐输出

```yaml
target_component: Dialog
regions:
  - name: dialog-header
    target: dialog-header
    mapping_status: mapped
    required_followup: none
    confidence: high
  - name: dialog-body
    target: dialog-content
    mapping_status: mapped
    required_followup: none
    confidence: high
    children:
      - name: dialog-body.form-section
        target: form-layout
        mapping_status: mapped
        required_followup: none
        confidence: high
      - name: dialog-body.help-copy
        target: business-layout
        mapping_status: layout-only
        required_followup: none
        confidence: medium
  - name: dialog-footer
    target: footer-actions
    mapping_status: mapped
    required_followup: none
    confidence: high
```

## 错误输出示例

```yaml
target_component: Dialog
implementation_plan:
  - 直接实现弹窗内容
```

这会丢失 body 内部的结构约束。

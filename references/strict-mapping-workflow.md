# 先映射后实现

这份文档用于约束 agent 不要跳过映射直接开写。

## 目标

把设计输入转换成实现之前，必须先形成结构化映射结论。  
映射结论不完整时，允许先保守实现，但不允许假装已经完成映射。

## 硬性流程

1. 先识别页面级模式
   例如：认证流程页、列表页、弹窗、表单页、详情页

2. 再识别区块级模式
   例如：顶部导航、步骤条、筛选区、表格区、上传区、footer 操作区

3. 再识别组件级对象
   例如：`Button`、`Dialog`、`Input`

4. 最后识别组件库能力
   例如：`props`、`variants`、`slots`、`tokens`、`composition`

只有完成上面 1-4，才允许进入代码实现。

## 最低输出要求

最少也要写出：

- 固定标题 `**Mapping Report**`
- `page_pattern`
- `regions`
- `target_component`
- `confidence`
- `evidence`

推荐最小格式：

```md
**Mapping Report**

```yaml
page_pattern: ...
interaction_summary: ...
provider_source: ...
regions:
  - name: ...
    target: ...
    confidence: ...
    evidence:
      - ...
library_api_mapping:
  ...
uncertain_points:
  - ...
fallback_notes:
  - ...
implementation_plan:
  - ...
```
```

## 错误示例

错误做法：

- 直接给代码，没有 `Mapping Report`
- “我大概知道这是什么页面了，直接改吧”
- “看起来像按钮，所以先写一个按钮”
- “先把布局搭出来，后面再补映射”

这些都会导致最后只是在“抄图”，而不是“做设计到组件语义的转换”。

## 正确示例

正确做法：

**Mapping Report**

```yaml
page_pattern: identity-flow
interaction_summary: 点击下一步后进入结果页
provider_source: hybrid
regions:
  - name: top-navigation
    target: business-layout
    confidence: high
    evidence:
      - 顶部固定导航
      - 左侧返回、品牌、标题
  - name: next-button
    target: Button
    confidence: high
    evidence:
      - 主色实心按钮
      - 底部主操作
  - name: upload-panel
    target: business-upload-panel
    confidence: medium
    evidence:
      - 包含步骤文案、下载入口、上传占位区
library_api_mapping:
  props:
    size: large
  variants:
    intent: primary
uncertain_points:
  - 上传区是否需要接真实接口
fallback_notes:
  - 上传区真实上传能力仍需由组件库 provider 或项目封装确认
implementation_plan:
  - 重写页面主体结构
  - 保留目标组件库中的主操作按钮
```

然后再开始写代码。

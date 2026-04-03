# Dialog 内容区继续映射规则

这份文档用于补齐 `figma-component-mapper` 在 dialog 场景下只识别外层容器、不继续拆 body 的问题。

核心要求：`Dialog` 不是终点，只是第一层识别结果。  
一旦识别出 dialog，必须继续完成三层识别：

1. 外壳层：`Dialog`
2. body 区块模式：表单型、说明确认型、列表选择型、复合业务型
3. body 内组件级对象：字段、提示、状态、图标、二级操作

## 基本原则

- body 不是“黑盒内容区”
- body 内关键字段、状态、提示、图标、二级操作都属于映射范围
- 不能因为 footer 已经识别清楚，就跳过 body 的继续拆解
- 若 body 内某部分不能直接映射为组件库组件，仍要明确标记为 `business-layout` 或 `custom-content`

## Dialog 三层识别顺序

### 第一层：外壳识别

满足越多，越像 `Dialog`：

- 页面中央浮层容器
- 顶部有标题或关闭入口
- 中间区域承载业务内容
- 底部存在取消/确定或其他操作按钮

输出时至少拆出：

- `dialog-header`
- `dialog-body`
- `dialog-footer`

### 第二层：body 区块模式

识别出 `dialog-body` 后，先判断它更像哪一类内容模式。

#### 表单型

典型信号：

- 多行 label + 输入控件排列
- 出现占位文本、下拉箭头、日期控件、勾选项
- 字段之间存在稳定上下间距

推荐落点：

- 表单容器
- 字段容器
- 输入类组件

#### 说明确认型

典型信号：

- 大段说明文案、提醒文案、风险提示
- 状态 icon 与提示文本并列
- 可能有二次确认复选框或补充说明

推荐落点：

- 文案块：`business-layout` 或说明区
- 状态区：反馈类 icon 语义
- 勾选项：按具体视觉映射到 checkbox 或业务布局

#### 列表选择型

典型信号：

- 表格、卡片列表、树形项或多行可选项
- 伴随搜索、筛选、批量选择

推荐落点：

- `Table`
- 卡片/树形业务布局
- 局部筛选控件

#### 复合业务型

典型信号：

- body 同时包含表单、提示、上传、预览、说明或卡片区
- 设计稿更像“布局 + 局部组件”的组合

推荐落点：

- 外层：`dialog-body` 下的 `business-layout`
- 局部可稳定识别的部分继续映射到具体组件

## 第三层：body 内组件级对象

`dialog-body` 继续拆解时，优先关注：

- 字段区：输入、选择、日期、开关、单选、多选
- 提示区：辅助说明、风险提醒、帮助文案
- 状态区：成功、警告、错误、处理中等 icon + 文案
- 二级操作：下载、查看示例、添加一行、删除一项
- 上传/预览区：上传占位、文件列表、缩略图、状态标签

每个关键子对象都必须二选一：

- 映射到组件或页面模式
- 标记为 `business-layout` / `custom-content`，并说明不能直接映射的原因

## 输出建议

建议在 `regions` 中这样表达：

```yaml
regions:
  - name: dialog-body
    target: dialog-content
    mapping_status: mapped
    required_followup: none
    confidence: high
    evidence:
      - 中间区域承载主要业务内容
    children:
      - name: dialog-body.form-section
        target: form-layout
        mapping_status: mapped
        required_followup: none
        confidence: high
      - name: dialog-body.tip-section
        target: business-layout
        mapping_status: layout-only
        required_followup: none
        confidence: medium
      - name: dialog-body.upload-section
        target: custom-content
        mapping_status: uncertain
        required_followup: identify-upload-pattern
        confidence: medium
```

## 错误示例 vs 正确示例

错误做法：

```yaml
target_component: Dialog
regions:
  - name: dialog
    target: Dialog
    confidence: high
implementation_plan:
  - 直接实现 dialog body DOM
```

问题：

- 只识别了外壳
- body 被当成黑盒
- 无法约束字段、提示、状态、图标的真实落点

正确做法：

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
      - name: dialog-body.tip-section
        target: business-layout
        mapping_status: layout-only
        required_followup: none
        confidence: medium
      - name: dialog-body.upload-section
        target: custom-content
        mapping_status: uncertain
        required_followup: identify-upload-pattern
        confidence: medium
  - name: dialog-footer
    target: footer-actions
    mapping_status: mapped
    required_followup: none
    confidence: high
```

## 与其他规则的配合

- 能力推断继续参考 [`visual-prop-inference.md`](visual-prop-inference.md)
- 页面模式优先级参考 [`page-patterns.md`](page-patterns.md)
- icon 继续识别参考 [`icon-recognition.md`](icon-recognition.md)

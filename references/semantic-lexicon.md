# 中英双语语义词典

## 使用原则

- 文案语义优先于图层命名
- 中文、英文、缩写、业务黑话都应先归一成通用语义
- 语义只提供候选，不直接替代组件判断

## 常见按钮语义

- 确定 / 提交 / 保存 / 完成 / Confirm / Submit / Save
  - 常见映射：`confirm` / `primary-action`
- 取消 / 返回 / 关闭 / Cancel / Back / Close
  - 常见映射：`cancel` / `dismiss`
- 新增 / 创建 / Add / Create / New
  - 常见映射：`create`
- 删除 / 移除 / Delete / Remove
  - 常见映射：`delete`
- 编辑 / 修改 / Edit / Update
  - 常见映射：`edit`
- 更多 / 展开 / More / Expand
  - 常见映射：`more` / `expand`

## 常见字段标签语义

- 名称 / 标题 / Name / Title
  - 常见映射：文本输入
- 类型 / 分类 / Type / Category
  - 常见映射：选择器
- 日期 / 时间 / Date / Time
  - 常见映射：日期时间选择类组件
- 状态 / Result / Status
  - 常见映射：状态标签或只读字段
- 备注 / 说明 / Remark / Description
  - 常见映射：多行输入或说明区

## 常见英文和缩写别名

- `pwd` -> password
- `desc` -> description
- `addr` -> address
- `cfg` -> configuration
- `msg` -> message
- `biz` -> business

## 常见 icon 语义

- 放大镜 -> search
- 加号 -> add
- 减号 / 垃圾桶 -> remove / delete
- 叉号 -> close / dismiss
- 勾号 -> success / confirm
- 感叹号 / 三角警示 -> warning / risk
- 眼睛 -> preview / view

## 冲突处理

- 文案语义与视觉语义冲突时，优先保留冲突说明，不要强行定论
- 文案只说明动作，不说明宿主时，继续结合布局与点击热区判断
- icon 语义存在多个候选时，先输出候选列表，再交给组件库 provider 对齐真实资产名

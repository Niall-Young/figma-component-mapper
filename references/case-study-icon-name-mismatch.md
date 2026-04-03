# 案例：Icon 设计名与开发名不一致时如何桥接

## 目标

避免看到图层名后直接硬猜真实资产名。

## 假设输入

- 右上角一个叉号图标
- 位于 dialog 标题栏
- 设计里命名为 `dismiss`
- 现有代码库可能并不使用这个名字

## 错误做法

- 直接把图层名当成最终资产名
- 不判断宿主与点击热区

## 正确推理

### 第一步：判断角色

- 这是可点击的 icon trigger

### 第二步：判断宿主

- 位于 dialog 标题栏，更像 `Dialog` 的关闭入口

### 第三步：产出语义候选

- `close`
- `dismiss`

### 第四步：桥接到开发候选资产

- 先输出候选，不直接定死真实资产名
- 交给组件库 provider 或项目先例桥接

### 第五步：确认最终落点

- 宿主仍是 `Dialog` 或其标题栏关闭动作

## 推荐输出

```yaml
icon_role: clickable-trigger
host_component: Dialog
icon_semantic_candidates:
  - close
  - dismiss
icon_asset_candidates:
  - close
  - dismiss
final_icon_mapping:
  host: Dialog
  attach_via: close-trigger
  semantic: close
fallback_notes:
  - exact asset name 需由组件库 provider 或项目先例确认
```

## 另一个变体

如果同样的图形出现在输入框内部右侧，正确输出就不应继续保留 `Dialog` 宿主，而应切换为：

- `host_component: Input`
- `icon_role: host-affix`

# 视觉特征到组件库能力的归一规则

## Button

### 能力优先顺序

1. `variants`
2. `props`
3. `tokens`
4. `composition`
5. `style override`

### 主次意图推断

- 主色实心背景 + 白字：优先尝试 primary / emphasized intent
- 次级描边：优先尝试 secondary / outline appearance
- 纯文字弱操作：优先尝试 text / ghost / subtle action

### 外观态推断

- 填充、描边、透明背景的差异，优先落到 appearance 类能力
- 不要在还没检查 appearance 前先写颜色和边框覆盖

### 形状推断

- 圆角变化优先查 shape、radius token、pill / rounded 变体

### 尺寸与 padding

- 高度、字号、左右 padding 若仍接近标准档位，先尝试 size
- 不要把 `padding` 和 `height` 覆盖当成 size 的替代方案

## Input

- 输入容器身份成立后，优先查 size、state、prefix/suffix
- 内嵌搜索图标时，先记录为“搜索态输入框”，再结合上下文决定宿主能力
- 边框、背景色、内边距变化若不影响输入框身份，不应直接否掉 `Input`

## Select

- 先查单选/多选、placeholder、state、trigger 外观
- 箭头样式或背景变化优先交给组件库能力，不要直接否掉 `Select`

## Dialog

- 先识别外壳，再拆 header/body/footer
- 宽度、居中、遮罩、关闭入口优先作为容器能力或布局能力记录
- body 内具体对象继续交给子区域映射，不要全部塞进同一层

## 说明

- 允许按语义映射，不要求视觉与组件库默认样式像素完全一致
- 若设计稿与默认样式存在细微差异，应写入 `deviation_notes`
- 不要为了贴设计稿去发明组件库中不存在的属性

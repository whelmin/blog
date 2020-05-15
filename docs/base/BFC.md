# BFC

2019 年 03 月 24 日

## 定义

`Block Formatting context`  （块级格式化上下文）

页面上的一个隔离的独立容器

是用于布局块级盒子的一块渲染区域

容器里面的子元素不会影响到外面元素

## 触发条件

1. 根元素，即 `HTML元素`
2. `float` 不为 none
3. `overflow` 不为 visible
4. `display` 的值为 `inline-block`，`table-cell`，`table-caption`
5. `position` 为 `absolute` 或者 `fixed`

## 布局规则

1. 内部的块级盒子会在垂直方向，一个接一个地放置
2. box 垂直方向的距离由 margin 决定。属于同一个 BFC 的两个相邻 Box 的 margin 会发生重叠。
3. 每个元素的 margin box 的左边， 与包含块 border box 的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此。
4. BFC 的区域不会与 float box 重叠

## 应用场景

1. 可以包含浮动元素，用于清除内部浮动
2. 分散在2个 BFC 中可阻止 margin 重叠

## 参考

[网易考拉前端团队-学习 BFC](https://juejin.im/post/59b73d5bf265da064618731d)
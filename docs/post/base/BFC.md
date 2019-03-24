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

## 参考

[网易考拉前端团队-学习 BFC](https://juejin.im/post/59b73d5bf265da064618731d)

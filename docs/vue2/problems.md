# Vue.js 常见问题整理

2018 年 10 月 01 日

## 一、data选项两种写法的区别

```data: {}``` 和 ```data() { return {}; }```

> 前者一般用于示例代码中，不会产生多个组件实例。

> 组件中使用 Vue 实例的数据对象，只接受 `function`，为了避免组件复用时，多个实例共享引用同一个数据对象。

## 二、ajax异步请求，props传值问题

父子组件通过 `props` 传值时，子组件渲染的 `template` 数据会自动响应 `props`

但若在生命周期函数中进行 `props` 处理，只能拿到 `props` 第一次的值。

子组件在生命周期函数中，取值会出现 `Cannot read property 'some' of undefined` 问题


  * 父组件传值进行 `v-if` 判断

  * 子组件使用 `watch` 来监听 `props`，使用 `methods` 来代替生命周期处理业务逻辑
# nextTick 用法和实现原理

?> 官方定义：一个用于下次 DOM 更新循环结束之后延迟执行回调的方法

?> 为什么会有这个方法：因为 Vue 在更新 DOM 时是异步执行的，数据改变后不会马上刷新视图

## 一、什么时候用

数据更新时，依赖最新 DOM 的操作需要使用 nextTick，如下代码：

```html
<div ref="msgDiv">{{ msg }}</div>
<div v-if="msg1">{{ msg1 }}</div>
<button @click="changeMsg"> Change the Message </button>
```

```javascript
changeMsg() {
	this.msg = "Hello world.";
	this.msg1 = this.$refs.msgDiv.innerHTML;
}
```

msg 更新时，其关联 DOM 不会立马更新，此时的 $refs.msgDiv.innerHTML 还是之前的 msg 数据

## 二、怎么用

### 1、参数，回调函数，可选

```javascript
this.$nextTick(() => {
	this.$refs.hui.innerText = "created hooks";
});
```

### 2、不传参数则在支持 Promise 的环境下，返回一个 Promise 实例

```javascript
this.$nextTick().then(() => {
  this.$refs.hui.innerText = "beforeMount hooks";
});
```

## 三、实现原理

1. 将同一 Tick，即同一批事件循环内，所有的 $nextTick 的回调函数存储在 callbacks 数组中

2. 根据当前环境支持的异步方法，使用对应的异步函数去循环处理 callbacks 数组
    1. 优先检测浏览器是否原生支持 **Promise**
    2. 其次检测是否支持 **MutationObserver**
    3. 检测是否支持原生 **setImmediate**
    4. 如果都不支持的话就会降级为 **setTimeout 0**

3. 还会判断如果缺失参数又支持 Promise，则提供一个 Promise 化的调用

```javascript
if (!cb && typeof Promise !== 'undefined') {
    return new Promise(resolve => {
    _resolve = resolve;
    })
}

nextTick().then(() => {});

// 当 _resolve 函数执行，就会跳到 then 的逻辑中。
```
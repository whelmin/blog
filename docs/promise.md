# Promise

2017年2月20日

## 含义和特点

`Promise`是异步编程的一种解决方案，比回调函数和事件合理和更强大，`es6的语言标准`

从语法上说，`Promise 是一个对象`，从它可以获取异步操作的消息。Promise 提供统一的 API，各种异步操作都可以用同样的方法进行处理。

**特点：**

对象的状态不受外界影响。`Promise`对象代表一个异步操作，有三种状态：`Pending`（进行中）、`Resolved`（已完成，又称 Fulfilled）和`Rejected`（已失败）。
Promise对象的状态改变，只有两种可能：从`Pending变为Resolved`和从`Pending变为Rejected`。
有了Promise对象，就可以将异步操作以同步操作的流程表达出来，**避免了层层嵌套的回调函数**。此外，Promise对象提供统一的接口，使得控制异步操作更加容易。

**缺点：**

1. 无法取消Promise，一旦新建它就会立即执行，无法中途取消。
2. 如果不设置回调函数，Promise内部抛出的错误，不会反应到外部。
3. 当处于Pending状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

## 基本用法

1. 创建

```javascript
var promise = new Promise(function(resolve, reject) {
  // ... some code
  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});
```

2. 指定回调函数

```javascript
// then 接受两个回调函数作为参数,第二个函数是可选的,不一定要提供
promise.then(function(value) {
  // success todo
}, function(error) {
  // fail todo
});

// 不要在then方法里面定义Reject状态的回调函数（即then的第二个参数），总是使用catch方法。
// good
promise
  .then(function(data) { //cb
    // success
  })
  .catch(function(err) {
    // error
  });
```
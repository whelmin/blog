# JS 异步编程

## 一、为什么JS要采用异步操作？

- 同步可能会阻塞进程，避免浏览器失去响应，耗时较长的操作都需要异步执行。
- JS语言设计为单线程。
- 提高CPU的利用率。

## 二、实现异步操作的几种方法

### 1. 回调函数

```
  f1();
  f2();
    
  // ===== 改写
  function f1(callback){
    setTimeout(function () {
      // f1的任务代码
      callback();
  　 }, 1000);
  }
  f1(f2);
```

**优点：简单、容易理解和部署**

**缺点：不利于代码的阅读和维护，各个部分之间高度耦合（Coupling），流程会很混乱，而且每个任务只能指定一个回调函数。**

### 2. 事件监听

采用事件驱动模式。任务的执行不取决于代码的顺序，而取决于某个事件是否发生。

以jQuery写法为例子

```javascript
  f1.on('done', f2);
  
  function f1(){
    setTimeout(function () {
      // f1的任务代码
    　　f1.trigger('done');
  　}, 1000);
  }
```

**优点：容易理解，可以绑定多个事件，每个事件可以指定多个回调函数，模块化**

**缺点：整个程序都要变成事件驱动型，运行流程会变得很不清晰**

### 3. 发布/订阅

发布/订阅模式（publish-subscribe pattern），又称观察者模式（observer pattern）。

```javascript
  jQuery.subscribe("done", f2);
  jQuery.unsubscribe("done", f2);
  
  function f1(){
    setTimeout(function () {
      // f1的任务代码
      jQuery.publish("done");
    }, 1000);
  }
```

### 4. Promise

#### 4.1 Promise是什么？

是一个对象，用来传递异步操作消息，它代表了某个未来才会知道结果的事件（通常是一个异步操作）、并且这个事件提供统一的API，可供进一步处理。

#### 4.2 解决了什么问题？

可以将异步操作以同步操作的流程表达出来，避免层层嵌套的回调函数。

#### 4.3 怎么用？

该构造函数接受一个函数作为参数，该函数的2个参数分别是 `resolve` 和 `reject`，他们是2个函数，由js引擎提供，不用自己部署。

```javascript
const promise = new Promise(function (resolve, reject) {
	if () { // 异步操作成功
		resolve(value);
	}
	else {
		reject(error);
	}
});
promise.then(function(value) {
	// success
}, function (value) {
	// fail
});
```

#### 4.4 优缺点是什么？

**优点：** 对象的状态不受外界影响，一旦状态改变，就不会再变，任何时候都可以得到这个结果。

**缺点：** 

1. 不能取消 Promise，一旦新建它就会立即执行。

2. 需要设置回调函数，Promise 内部抛出的错误才能反应给外部。

3. 当处于 pending 状态，无法的值目前进展到哪一步了。


### 5. async/await

#### 5.1 是什么？为什么会有这个语法糖？

为了解决 Promise 的 then 方法过多造成的代码冗余和语义不够清楚的问题，提出了 Generator 函数，它的 yield 表达式将异步操作表达得更清晰。async 函数实际上为 Generator 函数的语法糖，且内置了执行器。

Async - 定义异步函数(async function someName(){...})

- 自动把函数转换为 Promise
- 当调用异步函数时，函数返回值会被 resolve 处理
- 异步函数内部可以使用 `await`

Await - 暂停异步函数的执行 (const result = await someAsyncCall();)

- 当使用在 Promise 前面时，`await` 等待 Promise 完成，并返回 Promise 的结果。
- `await` **只能**和 Promise 一起使用，**不能**和 callback 一起使用。
- `await` 只能用在 `async` 函数中。

#### 5.2 怎么用？

```javascript
// 用Generator 函数实现依次读取2个文件

const fs = require('fs');
const readFile = function (fileName) {
  return new Promise(function (resolve, reject) {
    fs.readFile(fileName, function(error, data) {
      if (error) return reject(error);
      resolve(data);
    });
  });
};

const gen = function* () {
  const f1 = yield readFile('/etc/fstab');
  const f2 = yield readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};
```

```javascript
// 用async 函数实现依次读取2个文件
const asyncReadFile = async function () {
  const f1 = await readFile('/etc/fstab');
  const f2 = await readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};
```

#### 5.3 和generator有哪些不同？
  - async函数自带执行器，像普通函数一样加圆括号就执行 ，而Generator需要依靠内部的遍历器，调用遍历器的next才能一步步执行。
  - 更好的语义 async -> *，await -> yield。
  - 函数返回值是 Promise，可用then方法指定下一步操作。
    - Generator函数返回值 是一个遍历器对象。
  - 更广的适用性。
    - co模块约定，yield命令后面只能是 Thunk 函数或 Promise 对象。
    - async函数的await命令后面，可以是 Promise 对象和原始类型的值（数值、字符串和布尔值，但这时等同于同步操作）。

#### 5.4 有哪些写法？

```javascript
// 函数声明
async function fun1() {}

// 函数表达式
const fun1 = async function () {}

// 对象的方法
let obj = { async fun1() {}};
obj.fun1().then(...)

// class 
class Storage {
  constructor() {
    this.cachePromise = caches.open('avatars');
  }
  async getAvatar(name) {
    const cache = await this.cachePromise;
    return cache.match(`/avatars/${name}.jpg`);
  }
}
const storage = new Storage();
storage.getAvatar('jake').then(…);

// 箭头函数
const foo = async () => {};
```




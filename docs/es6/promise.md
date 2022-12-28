# Promise

# 一、是什么？

> 是一个用来***传递异步操作消息的对象***，它代表了某个未来才会知道结果的事件（通常是一个异步操作）、并且这个事件提供统一的 API，可供进一步逻辑处理。

# 二、怎么用？

> Promise 接受一个函数作为参数，该函数的 2 个参数是由 JavaScript 引擎提供的函数，分别是 resolve 和 reject，用于**传递异步操作成功的结果**和**异步操作失败时的错误**。使用 Promise 的实例方法 then 可指定成功和失败的回调函数。

```javascript
var promise = new Promise(function (resolve, reject) {
	if (typeof 1 === 'number') {
		resolve('success');
	}
	else reject(error);
});
// 构造函数接受一个函数作为参数，该函数的 2 个参数分别是 resolve 和 reject，
// 他们是2个函数，由 js 引擎提供，不用自己部署。
promise.then(function(value) {
	// fulfulled
	console.log('fulfulled', value);
}, function (error) {
	// rejected
	console.log('rejected', error);
});
// 实例生成后，可以用其实例 then 方法分别指定 resolved 状态和 rejected 状态的回调函数。
```

```javascript
const preloadImage = function (path) {
  return new Promise(function (resolve, reject) {
    const image = new Image();
    image.onload  = resolve;
    image.onerror = reject;
    image.src = path;
  });
};
```

# 三、解决了什么问题？

可以将异步操作以**同步操作的流程表达**出来，避免层层嵌套的回调函数。即避免回调地狱。

# 四、静态方法

## 1、Promise.prototype.then()

### 1. 入参，函数，为 Promise 实例添加状态改变时的回调函数。

> then 方法的第一个参数是 fulfilled 状态的回调函数，第二个参数（可选）是rejected 状态的回调函数。

```javascript
getJSON("/posts.json").then(function(json) {
	return json.post;
}, function(error) {
	console.log('Promise实例发生错误时会调用到这里');
});
```

### 2. 出参，返回的是一个新的 Promise 实例（注意，不是原来那个 Promise 实例），支持链式写法

> 使用 return 将需要传递的数据传递出去，可被下一个 then 接收到。
如果 return 了一个 Promise 对象，则下一个 then 就会等待该对象的状态发生变化，才会被调用。

```javascript
getJSON("/posts.json").then(function(json) {
	return json.post;
}).then(function(post) {
	// ...
});
```

```javascript
getJSON("/post/1.json").then(function(post) {
	return getJSON(post.commentURL);
}).then(function (comments) {
	console.log("resolved: ", comments);
}, function (err){
	console.log("rejected: ", err);
});
```

像几个存在依赖关系的异步请求，可一步步 return Promise 对象

```javascript
// 获取用户ID
function getUserId(url) {
    return new Promise(function (resolve) {
        http.get(url, function (id) {
            resolve(id);
        })
    })
}
// 获取用户的当前课程名称
function getNameById(id) {
    return new Promise(function (resolve) {
        http.get(getNameByIdUrl, function (name) {
            resolve(name);
        })
    })
}
// 获取课程ID
function getCourseByName(name) {
    return new Promise(function (resolve) {
        http.get(getCourseByNameUrl, function (course) {
            resolve(course);
        })
    })
}
// 获取课程详情
function getCourseDetailByCourse(course) {
    return new Promise(function (resolve) {
        http.get(getCourseDetailByCourseUrl, function (courseDetail) {
            resolve(courseDetail);
        })
    })
}
getUserId('some_url').then(function (id) {
    return getNameById(id);
}).then(function (name) {
    return getCourseByName(name);
}).then(function (course) {
    return getCourseDetailByCourse(course);
}).then(function (courseDetail) {
    // do something，展示在界面上
});
```

## 2、Promise.prototype.catch()

> Promise.prototype.catch()方法是.then(null, rejection)或.then(undefined, rejection)的别名，用于指定发生错误时的回调函数。

### 1. 入参，函数，捕获实例 rejected 状态的错误

### 2. 出参，无

### 3. Promise 对象的错误具有“冒泡”性质，会一直向后传递，直到被捕获为止。也就是说，错误总是会被下一个 catch 语句捕获。

> 但分不清错误到底发生在哪一个 Promise

## 3、Promise.prototype.finally()

> Promise.prototype.finally()方法用于指定不管 Promise 对象最后状态如何，都会执行的操作。finally 不确定 Promise 的状态，finally方法里面的操作，应该是与状态无关的，不依赖于 Promise 的执行结果。

### 1. 入参，无参数的函数

```javascript
promise
    .then(result => {···})
    .catch(error => {···})
    .finally(() => {···});
```

### 2. 出参，无

# 五、实例方法

## 1、Promise.resolve()

> 将现有对象转为 Promise 对象，并且状态是 fulfilled

### 1. 入参，四选一

- Promise 实例
- thenable 对象（thenable 对象指的是具有 then 方法的对象）
- 参数不是具有then方法的对象，或根本就不是对象
- 不带参数

### 2. 出参

- 入参是 Promise 实例时，原封不动地返回这个实例
- 入参是 thenable 对象时，返回由这个对象生成的 Promise 实例，然后立即执行 thenable 对象的then 方法。
- 入参是一个原始值，返回一个新的 Promise 对象，状态为 fulfilled
- 入参不带有任何参数，直接返回一个 fulfilled 状态的 Promise 对象。

## 2、Promise.reject()

> 将现有对象转为 Promise 对象，并且状态是 rejected

```javascript
const p = Promise.reject('出错了');
// 等同于
const p = new Promise((resolve, reject) => reject('出错了'));

p.then(null, function (s) {
    console.log(s); // 出错了
});
```

Promise.reject() 方法的参数，会原封不动地作为 reject 的理由，变成后续方法的参数。这一点与Promise.resolve 方法不一致。

```javascript
const thenable = {
    then(resolve, reject) {
        reject('出错了');
    }
};

Promise.reject(thenable)
.catch(e => {
    console.log(e === thenable); // true
})
```

## 3、Promise.all()

> Promise.all()方法用于将多个 Promise 实例，包装成一个新的 Promise 实例。所有实例变为 fulfilled 时，Promise.all()的状态才变为 fulfilled。只要有一个 rejected， Promise.all()的状态就变为 rejected

### 1. 入参，由 Promise 实例组成的数组

### 2. 出参，fulfilled 状态时传递由多个实例返回值组成的数组，rejected 状态时传递第一个reject实例的返回值。

```javascript
// 生成一个 Promise 对象的数组
const promises = [2, 3, 5, 7, 11, 13].map(id => getJSON(`/post/${id}.json`));

Promise.all(promises).then(function (posts) {
    // ...
}).catch(function(reason){
    // ...
});

// 上面 6 个 Promise 实例全部变为 fulfilled 状态，Promise.all 才会变为 fulfilled 状态，
// 将所有实例的结果以数组的形式传递出来。

// 上面 6 个 Promise 实例其中有一个变为 rejected 状态，Promise.all 会变为 rejected 状态，
// 将第一个 rejected 实例的结果传递出来，通常是错误。
```

## 4、Promise.race()

> Promise.race()方法同样是将多个 Promise 实例，包装成一个新的 Promise 实例。有一个实例率先改变状态，p的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给 p 的回调函数。若参数不是 Promise 实例，会调用 Promise.resolve() 将其转换为 Promise 实例

```javascript
// 传入参数为数字
const p = Promise.race([8, 9, 10]);

p.then(console.log)
.catch(console.error);

// 打印出数字 8
```

```javascript
// 传入参数为 thenable 对象

const thenable1 = {
    then: function(resolve, reject) {
        resolve('thenable1 fulfilled');
    }
};
const thenable2 = {
    then: function(resolve, reject) {
        reject('thenable2 rejected');
    }
};

Promise.race([thenable1, thenable2])
.then(console.log).catch(console.warn);
// 普通打印 thenable1 fulfilled

Promise.race([thenable2, thenable1])
.then(console.log).catch(console.warn);
// 警告打印 thenable2 rejected
```

## 5、Promise.allSettled()

> Promise.allSettled()方法接受一组 Promise 实例作为参数，包装成一个新的 Promise 实例。只有等到所有这些参数实例都返回结果，不管是fulfilled还是rejected，包装实例才会结束。返回的 Promise 实例最终结果永远是 fulfilled，结果永远是数组

```javascript
const resolved = Promise.resolve(42);
const rejected = Promise.reject(-1);

const allSettledPromise = Promise.allSettled([resolved, rejected]);

allSettledPromise.then(console.log);
// [
//    { status: 'fulfilled', value: 42 },
//    { status: 'rejected', reason: -1 }
// ]
```

## 6、Promise.any() - 提案

> Promise.any()方法接受一组 Promise 实例作为参数，包装成一个新的 Promise 实例。只要参数实例有一个变成 fulfilled 状态，包装实例就会变成 fulfilled 状态；如果所有参数实例都变成 rejected 状态，包装实例就会变成 rejected 状态。

```javascript
var resolved = Promise.resolve(42);
var rejected = Promise.reject(-1);
var alsoRejected = Promise.reject(Infinity);

Promise.any([resolved, rejected, alsoRejected]).then(console.log); // 42

Promise.any([rejected, alsoRejected]).catch(console.warn); // [-1, Infinity]
```

## 7、Promise.try() - 提案

> 统一同步函数和异步函数的写法，使同步函数同步执行，异步函数异步执行

# 六、有什么优点？

1. 对象的**状态不受外界影响。**

2. 一旦状态改变，就不会再变，任何时候都可以得到这个结果。

# 七、有什么缺点？

1. 不能取消 Promise ，一旦新建它就会立即执行。

2. 需要设置回调函数，Promise 内部抛出的错误才能反应给外部。

3. 当处于 pending 状态，无法确定目前进展到哪一步了。
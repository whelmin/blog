# ES6中的模块(module)

2018 年 10 月 17 日

## ES6之前

JavaScript世界一直没有模块概念，`ES6之前`是由社区给出了`2种模块加载方案`，**CommonJS(服务器) 和 AMD(浏览器)**。

并且模块之间的依赖关系，输入与输出都是**在运行时**决定的。

## ES6的模块Module

* 优点
  1. `不需要整体加载`模块。
  2. `编译时`就能分析依赖关系，可引用插件拓宽语法。
  3. 不再需要对象作为`命名空间`（比如Math对象）。
  4. 将来浏览器的新 API 就能用模块格式提供，不再必须做成全局变量或者navigator对象的属性。
  5. `自动采用严格模式`。

* 缺点
  1. 不能引用模块本身，因为模块本身不是对象。 

## export

```javascript
export let name = 'whelmin';
export let age = 18;

let name = 'whelmin';
let age = 18;
export {name, age};

export function multiply(x, y) {
  return x * y;
};

function v1() { ... }
function v2() { ... }
export {
  v1 as utilV1,
  v2 as utilV2,
  v2 as utilV3
};
```

## import

```javascript
import {firstName, lastName, year} from './profile.js';

import { lastName as surname } from './profile.js';
```

import命令输入的变量都是`只读的, 不可以改写`。(例外：如果该变量是对象的话，更改其属性是可以的。)

import命令`具有提升效果`，会提升到整个模块的头部，首先执行。

由于import是静态执行，所以`不能使用表达式、变量以及if结构`。

## export default 与 export

| 语法               | 说明                                                                   |
| :----------------- | :--------------------------------------------------------------------- |
| export default     | 用于指定模块的默认输出，`export default`命令在某个js文件中只能使用一次 |
| export             | 用于输出变量，函数和类，但是要按照指定格式进行输出。`{}`               |

```javascript
export default function crc32() {
};
import crc32 from 'crc32'; // 默认输出是crc32函数，给crc32函数重新命名为crc32

export function crc32() {
};
import {crc32} from 'crc32'; // 输出一个对象，其中有个属性是crc32函数
```

## 应用

* 跨模块常量，将常用的常量通过export输出，实现一个值要被多个模块共享的功能。

  1. 建立常量`constants`文件夹，分功能保存不同的常量。

  ```javascript
  // server.js
  export const server = {
    url: '',
    admin: '',
    pwd: ''
  };
  // users.js
  export const users = ['root', 'admin', 'staff', 'ceo', 'chief', 'moderator'];
  ```

  2. `index.js`进行常量文件的合并。

  ```javascript
  export {server} from './server';
  export {users} from './users';
  ```

  3. 使用`index.js`进行常量输出。

  ```javascript
  import {server, users} from './constants/index';
  ```

## 学习参考
  * [阮一峰 - ECMAScript 6 入门 - Module 的语法](http://es6.ruanyifeng.com/#docs/module)


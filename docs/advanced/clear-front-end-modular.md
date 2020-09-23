# 理清前端模块化

2019 年 04 月 05 日 清明节

## 模块化的进化

### 1. 全局 function

  函数即模块，调用时直接调用。

  ```javascript
  function mod1() {
    // 模块1
  }
  function mod2() {
    // 模块2
  }
  mod1();
  ```

  **缺点**: 污染了全局变量，容易命名冲突，模块成员关系不明确。

### 2. Object Method

  模块成员与方法都封装进对象内，调用时以对象属性形式调用。

  ```javascript
  const mod1 = {
    name: 'mod1',
    met1() {
      // 模块方法1
    },
    met2() {
      // 模块方法2
    },
  };
  mod1.name = null;
  console.log(mod1.name); // ✔  null
  mod1.met1(); // ✔
  ```

  **缺点**: 会暴露模块成员，内部属性可以被外部改写。

### 3. 立即执行函数（IIFE）

  > Immediately-Invoked Function Expression

  立即执行自调用匿名函数（闭包），可实现不暴露内部私有成员。

  ```javascript
  const mod1 = (() => {
    let name = 'mod1';
    let met1 = () => {
      // 模块方法1（内部保留)
    };
    let met2 = () => {
      // 模块方法2（暴露出去）
    };
    return {
      met2
    };
  })();
  console.log(mod1.name); // ✗  undefined
  mod1.met1(); // ✗  undefined is not a function
  mod1.met2(); // ✔ 能执行
  ```

  **缺点**: 闭包会带来内存泄漏问题。

### 4. 立即执行函数（IIFE） + 模块依赖

  一个模块需引用另一个模块时，可通过自调用函数的参数传入依赖变量。

  ```javascript
  const mod0 = jQuery;
  const mod1 = ((window, $) => {
    let name = 'mod1';
    let met1 = () => {
      // 模块方法1（内部保留）
    };
    let met2 = () => {
      // 模块方法2（暴露出去）
      console.log($('body'));
    };
    return {
      met2,
    };
  })(window, mod0);
  ```
  **缺点**: 请求过多、依赖模糊、难以维护。

  ```html
  <!-- mod1 没有依赖 -->
  <script src="mod1.js"></script>
  <!-- mod2 依赖于 mod1 -->
  <script src="mod2.js"></script>
  <!-- mod3 依赖于 mod2 -->
  <script src="mod3.js"></script>
  <!-- mod4 依赖于 mod1 mod3 -->
  <script src="mod4.js"></script>
  <!-- mod5 依赖于 mod2 mod3 mod4 -->
  <script src="mod5.js"></script>
  <script type="text/javascript">
    mod5.met(); // 调用 mod5 方法
  </script>
  ```

## 模块化规范

  IIFE / AMD / CMD / CommonJS / ESM / UMD

### 1. IIFE 规范

  立即自执行导出变量。

  ```javascript
  const mod1 = (() => {
    let name = 'mod1';
    let met1 = () => {
      // 模块方法1（内部保留）
    };
    let met2 = () => {
      // 模块方法2（暴露）
    };
    return {
      met2,
    };
  })();
  console.log(mod1.name); // ✗  undefined
  mod1.met1(); // ✗  undefined is not a function
  mod1.met2(); // ✔
  ```

### 2. AMD 规范(require.js)

  通过 `define` 方法，将代码定义为模块；通过 `require` 方法，实现模块加载。适合于浏览器环境，支持异步。

  依赖前置。

  ```javascript
  // mod1.js 
  // 定义 AMD 模块 mod1
  define([jQuery], ($) => {
    let met1 = () => $('body');
    return { met1 };
  });

  // main.js
  require(['mod1', 'mod2'], (mod1, mod2) => {
    mod1.met1();// 调用 mod1 方法
  });
  ```

  ```html
  <script src="lib/require.js" data-main="./main.js"></script>
  ```

### 3. CMD 规范(sea.js)

  通过 `define` 方法，将代码定义为模块；通过 `require` 方法，实现模块加载。适合于浏览器环境，支持异步。

  依赖就近。

  ```javascript
  // mod1.js 
  // 定义 CMD 模块 mod1
  define((require, exports, module) => {
    const $ = require('jQuery');
    let met1 = () => $('body');
    module.exports = { met1 };
  });

  // main.js
  require(['mod1', 'mod2'], (mod1, mod2) => {
    mod1.met1();// 调用 mod1 方法
  });
  ```

  ```html
  <script src="lib/sea.js"></script>
  <script type="text/javascript">
    seajs.use('./main.js');
  </script>
  ```

### 4. CommonJS 规范(node.js)

  在 `Node.js` 中的模块采用 `CommonJS` 规范，每个文件就是一个模块，有自己的作用域。

  在一个文件里面定义的变量、函数、类，都是**私有**的，对其他文件不可见。

  * 在**服务器端**，模块的加载是运行时**同步加载**的，并支持加载 **npm(node_modules)中的依赖包**；

  * 在**浏览器端**，模块需要**提前编译**打包处理。(搭配 browserify / grunt / gulp / webpack / rollup 等自动化工具)

  ```javascript
  // mod1.js
  // 定义 CommonJS 模块 mod1
  const $ = require('jquery');
  let met1 = () => $('body');
  module.exports = { met1 };
  ```

  ```javascript
  // main.js
  const mod1 = require('./mod1');
  mod1.met1();
  ```

### 5. ES Module 规范(ES6)

  `ES6` 带来了 `ES Module`，其设计思想是尽量静态化，使得编译打包时就确定模块间依赖关系，以及变量的输入和输出。这是不同于 `CommonJS` 和 `AMD` 规范的，只能在运行时动态确定依赖内容。

  因此 `ES Module` 的 `import` 必须写在文件顶部，而 `CommonJS` 的 `require` 可写在任意地方。

  ```javascript
  // mod1.js
  // 定义ES模块 mod1
  import $ from 'jquery';
  let met1 = () => $('body');
  export default { met1 };
  ```

  ```javascript
  // main.js
  import mod1 from './mod1';
  mod1.met1();
  ```

  > `ES6` 模块输出的是值的引用，而 `CommonJS` 是值的拷贝。

  * 值得注意的是，`Node.js` 虽然支持了大部分 `ES6` 语法，**但 `Node.js` 目前仍然未直接支持 `ES Module` 规范**，仍需 `Babel` 转译为 `CommonJS` 规范。

### 6. UMD 规范

  当在开发公共库时，不同的库使用者可能用着各种不同的模块化规范，而每种规范中模块的定义与返回都不一样，怎么办？

  `UMD` 是兼容多种模块化规范导出的一种规范，其打包后的格式大致为：

  ```javascript
  // UMD 导出格式
  (function (global, factory) {
      // 兼容 CommonJS
      typeof exports === 'object' && typeof module !== 'undefined' ? module.exports = factory() :
      // 兼容 AMD
      typeof define === 'function' && define.amd ? define(factory) :
      // 兼容 IIFE / 作用域变量 (LIB_NAME 为打包配置项)
      (global = global || self, global.LIB_NAME = factory());
      })(this, function () {
        // 模块打包出的内容
        return /* something */;
  });
  ```

  引入 `UMD` 规范的公共库时，支持直接使用 `CommonJS` / `AMD` / `IIFE <script>` 标签进行引入。

  可参考 [Vue 2.x 的源码](https://unpkg.com/vue@2.6.12/dist/vue.js)、[React的源码](https://unpkg.com/react@16.7.0/umd/react.production.min.js)

  > 但 `UMD` 不直接兼容 `ES Module` 规范。
  > 但好在，除了 `UMD`，工程化打包工具及其插件也是支持各个模块引入规范的转换。（例如：利用 `Webpack`，`ES6` 与 `CommonJS` 的模块引入区别几乎是无感的）


## CommonJS vs ES Module

### 定义一个 CommonJS 规范的模块

```javascript
// mod.common.js
const firstName = 'meimei';
const lastName = 'han';
const getName = () => firstName + lastName;
module.exports = { firstName, getName };
```

### 定义一个 ES Module 规范的模块

```javascript
// mod.esm.js
const firstName = 'meimei';
const lastName = 'han';
const getName = () => firstName + lastName;
export default { firstName, getName };
```

### 1. 统一用 CommonJS 规范引入不同规范的模块

```javascript
// main.common.js
const mod_common = require('./mod.common'); // CommonJS 规范的模块
const mod_esm = require('./mod.esm');       // ES Module 规范的模块
console.log(mod_common);
console.log(mod_esm);
```
👇👇👇

```javascript
// console.log(mod_common);
Object {
  firstName: 'meimei',
  getName: Function getName(),
}
```

```javascript
// console.log(mod_esm);
Module {
  default: Object {
    firstName: 'meimei',
    getName: Function getName(),
  },
  __esModule: true
}
```

### 2. 统一用 ES Module 规范引入不同规范的模块

```javascript
// main.esm.js
import mod_common from './mod.common';      // CommonJS 规范的模块
import mod_esm from './mod.esm';            // ES Module 规范的模块
console.log(mod_common);
console.log(mod_esm);
```
👇👇👇

```javascript
// console.log(mod_common);
Object {
  firstName: 'meimei',
  getName: Function getName(),
}
```

```javascript
// console.log(mod_esm);
Object {
  firstName: 'meimei',
  getName: Function getName(),
}
```

### 3. 总结单个导出模块 2 种规范的区别

  `CJS` 规范通过 `module.exports` 所导出的值为 `原值的拷贝` ；
  `ESM` 规范通过 `export` 所导出的值为 `Module` 对象 。

  两者规范混合使用时，经过 Babel 转换后，
  * 通过 `require` 引入的值即为导出的值： `CJS规范 - 原值的拷贝` ； `ESM规范 - Module对象` ；

  * 通过 `import` 引入的值将以 `Module` 对象的形式进行解析： `CJS规范 - (打包工具将转换为ESM规范)` ； `ESM规范 - Module对象` ；
  
### 4. 多个导出的模块

#### 4.1 CommonJS 规范的多个导出模块

```javascript
// mod.common.js
const firstName = 'meimei';
const lastName = 'han';
const getName = () => firstName + lastName;
module.exports = { firstName, getName };
module.exports.lastName = lastName;
```

```javascript
// mod_common module.exports
Object {
  firstName: 'meimei',
  getName: Function getName(),
  lastName: 'han'
}
```

```javascript
// CJS规范引用 mod_common：
const req_mod_common = require('./mod_common');
const lastName = req_mod_common.lastName; // 获取 lastName
const firstName = req_mod_common.firstName; // 获取 firstName
req_mod_common.getName(); // 调用 getName
// 或者 使用 [解构] 一次性获取 lastName firstName getName ：
const { lastName, firstName, getName } = require('./mod_common');

// EMS规范引用 mod_common：
import imp_mod_common from './mod_common';
// 得到的值 imp_mod_common 与 req_mod_common 是相等的
// 用法也与 req_mod_common 一样 略
// 同样也可使用 [多个导入] (并不是解构) 一次性获取 lastName firstName getName ：
import { lastName, firstName, getName } from './mod_common';
```

#### 4.2 ES Module 规范的多个导出模块

```javascript
// mod.esm.js
const firstName = 'meimei';
const lastName = 'han';
const getName = () => firstName + lastName;
export default { firstName, getName };
export { lastName };
```

```javascript
// mod_esm export
Module {
  default: Object {
    firstName: 'meimei',
    getName: Function getName(),
  },
  lastName: 'han',
  __esModule: true
}
```

```javascript
// CJS规范引用 mod_esm：
const req_mod_esm = require('./mod_esm');
const lastName = req_mod_esm.lastName; // 获取 lastName
const firstName = req_mod_esm.default.firstName; // 获取 firstName
req_mod_esm.default.getName(); // 调用 getName

// EMS规范引用 mod_esm：(不可使用解构)
import imp_mod_esm from './mod_esm';
const firstName = imp_mod_esm.firstName; // 获取 firstName
imp_mod_esm.getName(); // 调用 getName
// 得到的值 imp_mod_esm 与 req_mod_esm.default 是相等的
import { lastName } from './mod_esm'; // 获取 lastName
```

### 5. import 用法所对应的利用解构的 CommonJS 写法

```javascript
// 导出默认值, 即 获取 export default
import myDefault from './mod';
// 导出默认值, 上一行等价于:
import { default as myDefault } from './mod';
// 多个导出, 即 获取 export { foo, bar }
import { foo, bar } from './mod';
// 带有别名的导出, myFoo bar 将插入当前作用域
import { foo as myFoo, bar } from './mod';
// 导入整个模块的内容, myMod.default  myMod.foo 
import * as myMod from './mod';
// 多种联合导出
import myDefault, { foo as myFoo, bar }, * as myMod from './mod';
```
👇👇👇
```javascript
// 导出默认值, 即 获取 export default
const myDefault = require('./mod').default;
// 导出默认值, 上一行等价于:
const { default: myDefault } = require('./mod');
// 多个导出, 即 获取 export { foo, bar }
const { foo, bar } = require('./mod');
// 带有别名的导出, myFoo bar 将插入当前作用域
const { foo: myFoo, bar } = require('./mod');
// 导入整个模块的内容, myMod.default  myMod.foo 
const myMod = require('./mod');
// 多种联合导出
const myMod = require('./mod');
const myDefault = myMod.default;
const { foo: myFoo, bar } = myMod;
```

## npm包的模块规范

引用 npm 包时需注意的模块规范

### 1. import 和 require 是如何工作的？

```javascript
import lodash from 'lodash';
const lodash = require('lodash');
```

通过 [lodash的package.json](https://github.com/lodash/lodash/blob/master/package.json) 中的 `main` 字段，可以知道上方的引用指向 `npm` 包中的 `lodash.js` 文件。

👇👇👇
```javascript
import lodash from 'lodash/lodash.js';
const lodash = require('lodash/lodash.js');
```

通过代码可以看到 `lodash.js` 是打包后的 UMD 规范的全量文件，因此 `lodash.js` 可通过工程化工具直接引入打包，不需要经过 Babel 转译。

**思考**：但是，这种方式只能全量引入，如果要按需引入某个功能该怎么办？

### 2. 按需引入

#### 2.1 或通过路径找到需求的文件

```javascript
import chunk from 'lodash/chunk';
import map from 'lodash/map';
```

#### 2.2 或通过 babel 插件进行转换

```javascript
import { chunk, map } from 'lodash';
```

![babel-plugin-import](../../../_media/advanced/modular/babel-plugin-import.png)

#### 2.3 或利用 ES Module 的 tree-shaking

引入 `loadsh` 的 `esm` 规范版本的 `npm` 包

```javascript
import { chunk, map } from 'lodash-es’;
```

如何开启 `Tree Shaking` ？

* 需工程化工具支持（`Webpack` / `Rollup`），且 `npm` 包开发者需对 `package.json` 进行配置。

* `package.json/module` 字段 : 指明 `ES Module` 规范入口文件路径

* `package.json/sideEffects` 字段 : 标记 `Tree Shaking` 中副作用的文件路径 (有用的执行代码但并无导出)

### 3. 总结

`Webpack` 支持打包为 `umd` ，但暂不支持打包为 `esm`

`rollup` 支持打包为 `umd`、`esm` 两套

完整的 `npm` 包应考虑到：

1、理论上将打包出单个 `umd` 规范的独立文件就已经够用，是必须的；[antd@3.15.2/dist/](https://unpkg.com/antd@3.15.2/dist/)

2、有 `CJS` 规范需求，还可利用工程化工具生成仅转译后的文件树（`main`执向该文件树入口）；[antd@3.15.2/lib/](https://unpkg.com/antd@3.15.2/lib/)

3、同样，有 `ESM` 规范需求，可利用工程化工具生成仅转译后的文件树（`module`执向该文件树入口）；[antd@3.15.2/es/](https://unpkg.com/antd@3.15.2/es/)

4、当有按需引入的需求时，使用者可引入编译后文件树内的某个单文件；[antd@3.15.2/lib/card/index.js](https://unpkg.com/antd@3.15.2/lib/card/index.js) [antd@3.15.2/es/card/index.js](https://unpkg.com/antd@3.15.2/es/card/index.js)

5、对于 `ESM` 规范文件树，需考虑 `Tree Shaking` 情况；

6、引入未转译的源文件时，需提醒使用者加入 `babel` 进行转译。

## 学习参考

  * [阮一峰 - Javascript模块化编程（一）：模块的写法](http://www.ruanyifeng.com/blog/2012/10/javascript_module.html)

  * [阮一峰 - Javascript模块化编程（二）：AMD规范](http://www.ruanyifeng.com/blog/2012/10/asynchronous_module_definition.html)

  * [阮一峰 - Javascript模块化编程（三）：require.js的用法](http://www.ruanyifeng.com/blog/2012/11/require_js.html)

  * [阮一峰 - ES6 模块与 CommonJS 模块的差异](http://es6.ruanyifeng.com/#docs/module-loader#ES6-%E6%A8%A1%E5%9D%97%E4%B8%8E-CommonJS-%E6%A8%A1%E5%9D%97%E7%9A%84%E5%B7%AE%E5%BC%82)

  * [浪里行舟 - 前端模块化详解(完整版)](https://juejin.im/post/5c17ad756fb9a049ff4e0a62)
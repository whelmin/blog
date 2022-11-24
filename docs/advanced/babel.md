# Babel 是如何工作的

2020 年 09 月 03 日

## 一、 Babel 是什么？

读音：拜bo，一个**转码器**，可以将 ECMAScript 2015+ 版本的代码转换为向后兼容的 JavaScript 语法。

## 二、主要工作？

### 1、能转译哪些？

能转译**新标准引入的语法**，比如 ES6 中的箭头函数，let const，解构赋值，JSX语法，类型注释 Flow 和 TypeScript 

### 2、不能转译哪些？怎么办？

对于**新标准引入的全局变量**、部分原生对象新增的**原型链上的方法**，是不能被转译的。例子如下：

- 全局变量
    - Promise
    - Symbol
    - WeakMap
    - Set
- includes 方法
- generator 函数

通过 **Polyfill** 方式在目标环境中添加缺失的特性 (通过 **[@babel/polyfill](https://www.babeljs.cn/docs/babel-polyfill)** 模块)

### 3、编译过程

#### 1. Parsing - 先解析成 AST

将代码字符串解析成**抽象语法树**，Abstract Syntax Tree

抽象语法树是源代码的抽象语法结构的树状表示，整个解析过程被分为2个步骤，先进行**分词**，分成最小的语法单元，再进行**语法分析**，建立语法单元之间的关系。

这个阶段会用到 **[@babel/parser](https://www.babeljs.cn/docs/babel-parser)**

```jsx
import { parse } from '@babel/parser';
const code = 'class Example {}';
const ast = parse(code);
```

#### 2. Transformation - 再转换 AST

对抽象语法树进行**转换**操作，我的理解是根据指定的修改规则去修改AST（如 ES5 中不支持箭头函数，则在此阶段将箭头函数的AST用普通函数的AST代替）

这个阶段会用到 Plugins 和官方 Presets  **[@babel/preset-env](https://www.babeljs.cn/docs/babel-preset-env)**

*每年每个  Preset 只编译当年批准的内容。 而 babel-preset-env 相当于 ES2015 ，ES2016 ，ES2017 及最新版本。*

Plugins / Presets 调用优先级

- **Plugins 会运行在 Presets 之前**。
- **Plugins** 会从第一个开始**顺序执行**。
- **Presets** 的顺序则刚好相反(从最后一个**逆序执行**)。

#### 3. Code Generation - 把抽象 AST 生成代码字符串

根据转换后的抽象语法树再**生成**代码字符串以及源码映射（sourcemaps）。这个阶段会用到 **[@babel/generator](https://www.babeljs.cn/docs/babel-generator)**

![Babel工作过程](./_media/Babel工作过程.png)

#### 4. 一个例子

```javascript
console.log('helloworld');

// 1. 解析过程
//    - 1.1 分词结果
[
    {
        "type": "Identifier",
        "value": "console"
    },
    {
        "type": "Punctuator",
        "value": "."
    },
    {
        "type": "Identifier",
        "value": "log"
    },
    {
        "type": "Punctuator",
        "value": "("
    },
    {
        "type": "String",
        "value": "'helloworld'"
    },
    {
        "type": "Punctuator",
        "value": ")"
    },
    {
        "type": "Punctuator",
        "value": ";"
    }
]
//    - 1.2 语法分析，抽象语法树
{
  "type": "Program",
  "start": 0,
  "end": 206,
  "body": [
    {
      "type": "ExpressionStatement",
      "start": 179,
      "end": 205,
      "expression": {
        "type": "CallExpression",
        "start": 179,
        "end": 204,
        "callee": {
          "type": "MemberExpression",
          "start": 179,
          "end": 190,
          "object": {
            "type": "Identifier",
            "start": 179,
            "end": 186,
            "name": "console"
          },
          "property": {
            "type": "Identifier",
            "start": 187,
            "end": 190,
            "name": "log"
          },
          "computed": false
        },
        "arguments": [
          {
            "type": "Literal",
            "start": 191,
            "end": 203,
            "value": "helloworld",
            "raw": "'helloworld'"
          }
        ]
      }
    }
  ],
  "sourceType": "module"
}
```

## 三、主要版本？

1. @babel/*，7.0.0 以上
2. babel-*，7.0.0 以下，最高版本为 6.26.3

## 四、有哪些包？

以 Babel 7 分析

### 1、基础类

1. @babel/cli
2. @babel/polyfill（已废弃）
3. @babel/plugin-transform-runtime
4. @babel/register

### 2、预设类

1. @babel/preset-env
2. @babel/preset-stage-0（已废弃）
3. @babel/preset-stage-1（已废弃）
4. @babel/preset-stage-2（已废弃）
5. @babel/preset-stage-3（已废弃）
6. @babel/preset-flow
7. @babel/preset-react
8. babel-preset-minify
9. @babel/preset-typescript

### 3、工具类

1. @babel/parser
2. @babel/core
3. @babel/generator
4. @babel/code-frame
5. @babel/helpers
6. @babel/runtime
7. @babel/template
8. @babel/traverse，用于对 AST 的遍历，维护了整棵树的状态，并且负责替换、移除和添加节点。
9. @babel/types，用于 AST 节点的 Lodash 式工具库, 它包含了构造、验证以及变换 AST 节点的方法，对编写处理 AST 逻辑非常有用。

## 五、常见问题

### 1. Promise polyfill 是如何实现的？

> 对于新标准引入的全局变量、部分原生对象新增的原型链上的方法，是不能被转译的。如 Promise

待补充

### 2. babel 是如何进行类型检查的？

官方文档有提到，Babel 不做类型检查，可以删除类型注释，需要借助预设 babel-preset-flow 和 babel-preset-typescript。

那咱们的类型检查，拿 Typescript 来说，我的理解应该是在生成 AST 后，**遍历 AST 过程中进行类型推断和检测**，有错误则直接抛出，停止继续编译。参考 recast 工具的 types.namedTypes 方法也是在生成 AST 之后，在 recast.visit 遍历 AST 过程中调用的。

## 六、附录

- [前端工程师需要了解的 Babel 知识](https://www.zoo.team/article/babel)

- [不容错过的 Babel7 知识](https://juejin.im/post/5ddff3abe51d4502d56bd143)

- [前端工程师的自我修养-关于 Babel 那些事儿](https://juejin.im/post/5e5b488af265da574112089f)

- [AST抽象语法树--最基础的javascript重点知识，99%的人根本不了解](https://segmentfault.com/a/1190000016231512)
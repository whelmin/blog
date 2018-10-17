# ES6入门

2018年03月18日

?> 面对"了解es6吗？es6 与 es5 有什么区别？es6有哪些新特性"等问题时，可以从下面3个方面进行回答，分别是 **语法**、**API**、**特性**

## 1. 语法上

1. 增加`块级作用域`
2. 新增 **let** **const**关键字
  > let, const的区别:
  > 一个声明是变量、一个声明是常量，const再次赋值会报错。
  > 但对于`只会改变内部属性值`的一些 **对象**，还有 **数组**，本身(引用地址)是没有改变的，可以`用const进行声明`）
  
3. 箭头函数

  ES6 允许使用箭头（=>）定义函数。

  如果箭头函数`不需要参数`或`需要多个参数`，就使用`一个圆括号`代表参数部分。

  ```javascript
  var f = v => v;
  // 等同于
  var f = function (v) {
    return v;
  };
  var f = () => 5;
  // 等同于
  var f = function () { return 5 };
  ```

4. 解构赋值（按照一定的模式，从数组和对象中提取值，对变量进行赋值）

5. 模板字符串，在es5中是通过加号来拼接字符串(写法繁琐)，在es6中是通过`反引号`直接将变量和其他字符串拼接起来。

  ```javascript
  `${name}: whelmin`
  ```

## 2. API

对常见的数据类型都进行了扩展，如`Object`, `Array`, `String`等

### Object扩展

* `Object.is()`
* `Object.assign()`

### Array扩展

* `Array.from()`
* `Array.includes()`
* `Array.of()`
* `Array.copyWithin()`
* `Array.find()`
* `Array.findIndex()`

### String扩展

* `Object.is()`
* `Object.assign()`

## 3. 特性

  1. 新增Set、Map数据结构，可利用Set进行数组的去重
  2. 新增Symbol类型
  3. 新增异步promise

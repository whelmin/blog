# Class

2018 年 07 月 07 日 星期天

?> `ES5` 中通过 `new` 构造函数来生成实例对象，通过构造函数的 `prototype` 来实现继承，但写法会困惑新手。

## 1. 写法

### 1.1 ES6 的 Class 写法

```javascript
class Person {
  constructor(lastname, firstname) {
    this.lastname = lastname;
    this.firstname = firstname;
  }
  getFullname() {
    return `${this.lastname} ${this.firstname}`;
  }
}
```

等同于

```javascript
Person.prototype = {
  constructor() {},
  getFullname() {},
};
```

类的所有方法都定义在类的``prototype``属性上面。

```javascript
Person === Person.prototype.constructor // true
```

生成实例

```javascript
let mingming = new Person('ming', 'ming');

mingming.getFullname(); // ming ming

mingming.constructor === Person.prototype.constructor // true
```

### 1.2 ES5 的构造函数写法

```javascript
function City(name, count) {
  this.name = name;
  this.count = count;
}

City.prototype.getCityDes = function () {
  return `${this.name}: ${this.count} 人`;
};
```

```javascript
City === City.prototype.constructor // true
```

生成实例

```javascript
let shenzhen = new City('shenzhen', '110k+');

shenzhen.getCityDes(); // shenzhen: 110k+ 人

shenzhen.constructor === City.prototype.constructor // true
```

## 2. 区别

### 2.1 类中的实例方法不可枚举

ES6 的 Class

```javascript
Object.keys(Person.prototype); // [] getFullname 方法是 Person类 内部定义的方法，它是不可枚举的。
Object.getOwnPropertyNames(Person.prototype); // ["constructor", "getFullname"]
```

ES5 的 构造函数

```javascript
Object.keys(City.prototype); // ["getCityDes"] getCityDes 是可枚举的。
Object.getOwnPropertyNames(City.prototype); // ["constructor", "getCityDes"]
```

### 2.1 调用方式

ES6 的 Class， 必须和 `new` 搭配使用，而 ES5 的 构造函数 不使用 `new` 也可以被执行，不报错。

## 4. 学习参考

  [阮一峰 - ECMAScript 6 入门](http://es6.ruanyifeng.com/#docs/class)

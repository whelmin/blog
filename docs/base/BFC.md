# JavaScript 数组去重

2017 年 10 月 26 日

> JavaScript 数组去重是前端面试中经常会问到的一个问题，面试官考察的是解法的`执行效率`以及`时间复杂度`吧。
> 自己最容易想到的可能就是第一种去重前后两个数组`双重循环`判断。

## 1. 两个数组双重循环判断

```javascript
Array.prototype.unique0 = function() {
  console.info(new Date().getTime() * 1000);
  // 新建去重后的数组
  var resultArr = [],
    isUnique;
  for (var i = 0, length = this.length; i < length; i++) {
    // 初始是唯一的
    isUnique = true;
    for (var j = 0; j < resultArr.length; j++) {
      if (this[i] === resultArr[j]) {
        isUnique = false;
        break;
      }
    }
    if (isUnique) {
      resultArr.push(this[i]);
    }
  }
  // console 检查for运算所用时间
  console.info(new Date().getTime() * 1000);
  return resultArr;
};
```

## 2. indexOf 判断

```javascript
Array.prototype.unique1 = function() {
  var resultArr = [];
  for (var i = 0, length = this.length; i < length; i++) {
    if (resultArr.indexOf(this[i]) == -1) {
      // indexOf 表示指定字符串值在字符串的首次出现位置，
      //-1表示该指定字符串值不存在
      resultArr.push(this[i]);
    }
  }
  return resultArr;
};
```

## 3. hashtable 判断

```javascript
Array.prototype.unique2 = function() {
  console.info(new Date().getTime() * 1000);
  var resultArr = [],
    // n 为 hash 表
    n = {};
  for (var i = 0, length = this.length; i < length; i++) {
    if (!n[this[i]]) {
      n[this[i]] = true;
      resultArr.push(this[i]);
    }
  }
  console.info(new Date().getTime() * 1000);
  return resultArr;
};
```

## 4. 先排序再循环判断

```javascript
Array.prototype.unique3 = function() {
  console.info(new Date().getTime() * 1000);
  this.sort();
  var resultArr = [this[0]];
  for (var i = 1, length = this.length; i < length; i++) {
    if (this[i] !== resultArr[resultArr.length - 1]) {
      resultArr.push(this[i]);
    }
  }
  console.info(new Date().getTime() * 1000);
  return resultArr;
};
```
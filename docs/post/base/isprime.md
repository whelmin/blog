# 判断一个数是否为素数（质数）

2017 年 03 月 26 日

> 只能被 1 或者自身整除的数，我们称它为质数。

## 1. 方法 1

我们只需要循环判断这个数除以比它小的每个自然数，如果有被整除的，则不是质数

```javascript
function isPrime(num) {
  if (num <= 1) {
    return false;
  } else if (num == 2) {
    return true;
  } else {
    for (var i = 2; i <= num - 1; i++) {
      return num % i == 0 ? false : true;
    }
  }
}
```

缺点：这个数数值为 n，就要循环 n 次。

## 2. 数学定理的运用

> 如果一个数是合数，那么它最小的质因数肯定不大于它的平方根。
> （一个大于 1 的自然数如果不是合数就是质数）

我们只需要判断它能否被不大于它的平方根的所有数整除，（也就是找一下有没有最小质因数）如果没有被整除的，说明是质数。

```javascript
function isPrime(num) {
  if (num <= 1) {
    return false;
  } else if (num == 2) {
    return true;
  } else {
    var sqrtedNum = Math.floor(Math.sqrt(num) + 1);
    for (var i = 2; i <= sqrtedNum; i++) {
      return num % i == 0 ? false : true;
    }
  }
}
```

## 如何筛选出给定区间内的质数

```javascript
function productPrime(range) {
  //将一个数组的值都置为true，默认是质数
  var arr = Array.apply(null, { length: range }).map(function() {
    return true;
  });
  var myarr = new Array();
  arr[0] = false; // 1不是质数，存储在位置0上
  var sqrtedNum = Math.floor(Math.sqrt(range) + 1);
  for (var j = 2; j <= sqrtedNum; j++) {
    if (isPrime(j)) {
      for (var k = j; k * j <= range; k++) {
        // 质数的倍数一定是合数
        arr[k * j - 1] = false;
      }
    }
  }
  for (var i = 0; i < range; i++) {
    if (arr[i] == true) {
      myarr.push(i + 1);
    }
  }
  return myarr;
}
console.log(productPrime(10)); //结果为 [2，3，5，7]
```

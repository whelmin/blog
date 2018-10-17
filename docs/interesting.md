# 一些有趣的编程题

2016年5月4日


## 1. 序列组合最大

**给定一个正整数序列，请尝试将它们重新排列，使得排列的结果最大。举例来说，比如3，31，35，7，9，根据排列组合，得出的值应该是9735331.最终这个值可能会超过int的范围，所以返回的类型应该是string，即输出的是字符串。**

假设两个数，a，b（转换为字符串）拼接得到ab和ba，如果ab > ba，则a排在前面，如果ab < ba，则返回时b排在前

```javascript
function getMax(s){
  // 传入要比较的数组
  var resultArr = [];
  for(var i = 0, length = s.length; i < length; i++) {
    resultArr.push(s[i].toString());
  }
  resultArr.sort(function(a, b) {
    // 使排列大的字符串排前面
    return (b+a) - (a+b);
  });
  return resultArr.join("");
}
getMax([9,31,35,3,7,98]);// "998735331"
getMax([9,31,35,3,7]); // "9735331"
```

## 2. 按照属性个数给object数组排序

**给一个object数组排序，按照object的属性个数，其中属性不包括原型链上的属性。**

利用hasOwnProperty找到object的属性（不包括原型链上的属性）

```javascript
function Sortby(arr) {
  for(var i = 0,length = arr.length; i<length; i++) {
    var count = 0;
    for(var k in arr[i]) {
      if(arr[i].hasOwnProperty(k)) {
        count++;
       }
      arr[i].length = count -1 ; 
      // 给object增加一个length表示属性个数，但是length也会成为自定义属性，所以要-1
    }
  }
  arr.sort(function(a,b){
    return a.length - b.length; // 升序
  });
  return arr;
}
```

或者

```javascript
function sortObjectArray(arrays) {
  return arrays.sort(compareAsc);
}
function compareAsc(a, b) {
  return getPropertyLength(a) - getPropertyLength(b); //升序
}
function getPropertyLength(obj) {
  var length = 0,k;
  for(k in obj) {
    if(obj.hasOwnProperty(k) && typeof obj[k]!== 'function') length++;
  }
  return length;
}
```

验证结果

```javascript
var arr33 = [{a:1, b:2},{a:2, b:3},{a:33, c:22, d:33},{c:1}];
Sortby(arr33)[0] Object {a: 1, b: 2, length: 2}
Sortby(arr33)[1] Object {a: 2, b: 3, length: 2}
Sortby(arr33)[2] Object {a: 33, c: 22, d: 33, length: 3}
Sortby(arr33)[3] Object {c: 1, length: 1}
```

## 3. 数组嵌套数组进行排序

**一个数组 A = [6,3,5,8,4,9,1,[6,3,5,8,4,9,1,[6,3,5,8,4,9,1]]] 用简短的代码给该数组里面的数字排序**

### 3.1 理解1 嵌套数组需要拆开

```javascript
var A = [6,3,5,8,4,9,1,[6,3,5,8,4,9,1,[6,3,5,8,4,9,1]]];
A.join(',').split(',').sort();
// ["1", "1", "1", "3", "3", "3", "4", "4", "4", "5", "5", "5", "6", "6", "6", "8", "8", "8", "9", "9", "9"]
A.join(',');
// "6,3,5,8,4,9,1,6,3,5,8,4,9,1,6,3,5,8,4,9,1"
A.join(',').split(',');
// ["6", "3", "5", "8", "4", "9", "1", "6", "3", "5", "8", "4", "9", "1", "6", "3", "5", "8", "4", "9", "1"]
```

### 3.2 理解2 嵌套数组不拆开

```javascript
function handle(a, b) {
  if(Array.isArray(a)|| Array.isArray(b)) {
    if(Array.isArray(a)){
      a.sort(handle);
    }else {
      b.sort(handle);
    }
  }
  else{
    return a - b;
  }
}
A.sort(handle);
// [1,3,4,5,6,8,9[1,3,4,5,6,8,9[1,3,4,5,6,8,9]]];
```
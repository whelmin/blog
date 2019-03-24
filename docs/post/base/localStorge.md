- [localStorge,sessionStorge,cookie](#localstorgesessionstorgecookie)
  - [写在前面](#%E5%86%99%E5%9C%A8%E5%89%8D%E9%9D%A2)
  - [cookie](#cookie)
  - [localStorge](#localstorge)
  - [sessionStorge](#sessionstorge)
  - [区别](#%E5%8C%BA%E5%88%AB)
    - [1. 生命周期](#1-%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F)
    - [2. 存储大小](#2-%E5%AD%98%E5%82%A8%E5%A4%A7%E5%B0%8F)
    - [3. http 请求](#3-http-%E8%AF%B7%E6%B1%82)
    - [4.易用性](#4%E6%98%93%E7%94%A8%E6%80%A7)
    - [5. 支持情况](#5-%E6%94%AF%E6%8C%81%E6%83%85%E5%86%B5)

# localStorge,sessionStorge,cookie

2016 年 04 月 27 日

## 写在前面

网页刷新时，要保持原有数据或者状态，需要利用到本地存储技术，比较常见的是 `cookie`，`localstorge`，`sessionstorge`

## cookie

```javascript
document.cookie; // _ga=GA1.1.1022170368.1543221879; avatar=; username=admin; sidebarStatus=1
```

## localStorge

属于 window 对象

```javascript
localStorge.setItem(key, value); // set
localStorge.getItem(key); // get
localStorge.removeItem(key); // remove
localStorge.clear(); // clear
localStorge.valueOf(); // get all
```

## sessionStorge

属于 window 对象, 和 localStorge 用法一致

## 区别

### 1. 生命周期

- cookie 可设置 `expires` 过期时间，没有设置则为 关闭浏览器 就失效
- localStorage 需要手动清除
- sessionStorge 当前网页有效，关闭则被清除（关闭窗口 或者是 当前标签页）

### 2. 存储大小

- cookie 4kb
- localStorage （5M 左右）
- sessionStorge （5M 左右）

### 3. http 请求

- cookie 自动携带在 http 头中，cookie 如果保存过多，会造成性能问题
- localStorage 和 sessionStorge，不参与 http 请求

### 4.易用性

- cookie 需要 自己封装
- localStorage 和 sessionStorge，原生接口比较好用

### 5. 支持情况

- localStorage 和 sessionStorge 为 HTML5 新特性

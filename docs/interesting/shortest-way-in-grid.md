# 左上角到右下角的最短网格路径

> 在美团的笔试题中发现了好玩的东西，如下网格问题，需要涉及到数学的`递归`问题。其实最最重要的还是`算法`，`规律`的发现，苦于这一点，查阅了资料，终于搞清楚了这个最短的网格路径问题。

## 题目

**从一个 x \* y 的网格的左上角出发，只允许向右或者向下行走，问到达（x , y）这个点，有多少条路径。**

只允许向右或者向下行走，其实也就是最短的路径。这个题目也可以概括为『 x \* y 的网格从左上角到某个点的最短路径的总数』

### 方法 1

直接`暴力枚举`，希望找出规律，然后用数学表达式解决，然后没有然后了，自己数着数着就数不清了。

### 方法 2

从`网格的右上角`入手，`通过正方形个数`（从 0 开始），试着找规律，然后没有然后了，自己数着数着又数不清了。

### 方法 3

[Google 一下，转换一下思维，规律一下就找到了](https://blog.oldj.net/2013/09/07/lattice-paths/)

![grid](_media/grid.jpg)

绘制一个大的网格，横向表示 x 坐标，纵向表示 y 坐标，网格的交点坐标为（x , y）。

蓝色的表示从（0 , 0）到该点的走法总和，观察我们可以指导各个交点的走法等于它上方以及左方交点之和。
用函数 routecount(x , y)来表示到达交点的走法，我们可以总结出来。

```
                    | 0,(x =y =0);
routecount(x , y) = | 1,(x = 0 or y = 0)
                    | routecount(x-1 , y) + routecount(x , y-1)
```

我们很快的写出代码来

```javascript
function routecount(x, y) {
  var sum;
  // 对x,y的判断
  if (x > y) {
    var temp = x;
    x = y;
    y = temp;
  }
  if (y <= 0) {
    return 0;
  }
  if (x == 0 || y == 0) {
    sum = 1;
  } else if (x == 0 && y == 0) {
    sum = 0;
  } else {
    sum = arguments.callee(x - 1, y) + arguments.callee(x, y - 1);
  }
  return sum;
}
console.log(routecount(2, 2)); //结果为 6
console.log(routecount(3, 6)); //结果为 84
```
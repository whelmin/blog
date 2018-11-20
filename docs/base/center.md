# 纯CSS实现各种居中

2016年09月08日

> 用CSS实现两个大类的居中，分为`水平`居中和`竖直垂直方向`居中。其中水平居中细分又分为`行内元素`，`定宽定高的块级元素`居中，竖直居中分为`父元素高度确定与否`的居中，`单行文本`和`多行文本`的垂直居中，下面我们来看一下啦~

## 水平居中

1. **行内元素**，例如图片，文本等行内元素，对其`父元素`设置：

  ```css
  text-align: center;
  ```

2. **确定宽度的块级元素**，只需对`该元素`设置：

  ```css
  margin: 0 auto;
  ```

  或者使用 **绝对定位** 设置：
  
  ```css
  该元素 {
    position: absolute;
    left: 50%;
    margin-left: half-width;
  }
  其父元素 {
    position: relative;
  }
  ```
3. **不确定宽度的块级元素**

  ① **table**方法
  > 由于table不是块级元素，宽度是由内部的元素撑起，对其设置即可，*缺点就是会增加无语义的标签*

  ```css
  其父元素 {
    display: table;
  }
  该元素 {
    display: table-cell;
    text-align: center;
  }
  ```

  ② **inline-block**方法
  > 将其设置为行内元素，通过行内元素的居中方式居中，*缺点：行内元素本身少了一些设置功能*

  ```css
  display: inline-block;
  text-align: center;
  ```

## 垂直居中

1. **单行文字垂直居中**
  对自身设置：

  ```css
  line-height: box-height; //box-height为你需要的高度。
  ```

2. **多行文字垂直居中**
  自身高度不固定：

  ```css
  padding-top = padding-bottom;
  ```

3. **父元素高度确定的多行文本，图片，块级元素**
  ① **伪元素**方法

  ```css
  // 其父元素
  text-align: center;
  // 父元素的伪元素
  父元素:before
  content: '';
  display: inline-block;
  height: 100%;
  vertical-align: middle;
  margin-right: -0.25em;(消除inline-block带来的空白间距)
  // 该元素
  display: inline-block;
  vertical-align: middle;
  ```

  ② **table**方法
  只支持ie8 & firefox 要使用特定的hack实现兼容

  ```css
  // 其父元素
  display: table;
  width: 100%;
  // 该元素
  display: table-cell;
  vertical-align: middle;
  ```

  ③ **绝对定位**方法

  ```css
  // 该元素
  position: absolute;
  top: 50%;
  margin-top: half-height;
  // 其父元素
  position: relative;
  ```

  ④ **transform**方法

  ```css
  // 该元素
  margin: 50% auto 0;
  transform: translateY(-50%);
  或者
  margin: 0 auto 50%;
  transform: translateY(50%);
  ```

  ⑤ **Flexbox**方法

  ```css
  // 其父元素
  display: flex;
  // 该元素
  margin: auto;
  ```

## 举几个栗子🌰

1. **弹出层水平垂直居中**

  ```css
  // 该元素
  position: fixed;
  top: 50%;
  left: 50%;
  width:50%;
  height: 50%;
  background-color: #ccc;
  -webkit-transform: translateX(-50%) translateY(-50%);
  -moz-transform: translateX(-50%) translateY(-50%);
  -ms-transform: translateX(-50%) translateY(-50%);
  transform: translateX(-50%) translateY(-50%);
  ```

2. **css3定宽高水平垂直居中**

  ```css
  // 其父元素
  justify-content:center;
  align-items:center;
  display:-webkit-flex;
  display:flex;
  width: number;
  height: number;
  // 该元素
  width: number;
  height: number;
  ```
# 浮动float
```CSS
float: left; // 元素脱离文档流，左浮动
float: right; // 元素脱离文档流，右浮动
float: none; // 默认值。元素不浮动
float: inherit; // 从父元素继承 float 属性的值
```

## 浮动的特性
+ `脱离文档流`：浮动元素会脱离文档流，并向左/右 浮动，直到碰到父元素或者另一个浮动元素
+ `不影响之前渲染好的元素`：float不影响前面已经渲染好的文档，而会影响在其后面将要渲染的文档
+ `不遮挡文本`：float不会遮挡文本，因为设置初期就是为了实现文字环绕


## 如何解决浮动带来的影响？

### 一、给父元素设置高度
```HTML
<style>
    .parent {
        height: 200px;
        background: blue;
    }
    .child {
        height:200px;
        width: 50px;
        background: red;
        float: right;
    }
</style>

<div class="parent">
    <div class="child"></div>
</div>
```
[在线预览](https://codepen.io/ptcp3/pen/MWQeoeE)
+ **优点**
    + 简单快速
    + 代码少
+ **缺点**
    + 无法进行响应式布局

### 二、利用BFC的特性
**父级定义`overflow: hidden;`**
```HTML
<style>
    .parent {
        overflow: hidden;
        background: blue;
    }
    .child {
        height:200px;
        width: 50px;
        background: red;
        float: right;
    }
</style>

<div class="parent">
    <div class="child"></div>
</div>
```
[在线预览](https://codepen.io/ptcp3/pen/vYdKZXq)
+ **优点**
    + 简单快速
    + 代码少
    + 兼容性高

+ **缺点**
    + 超出部分被隐藏，布局时要特别注意

使用BFC来解决的方法，还有很多，所有可以让元素变为BFC的方法都可以使用，[详情见](./01BFC%E5%92%8CIFC.md)

### 三、clear: both
```HTML
<style>
    .parent {
        background: blue;
    }
    .child {
        height:200px;
        width: 50px;
        background: red;
        float: right;
    }
    .clear {
        clear: both;
    }
</style>

<div class="parent">
    <div class="child"></div>
    <div class="clear"></div>
</div>
```
[在线预览](https://codepen.io/ptcp3/pen/ExQyXWp)
+ 优点
    + 简单快速
    + 代码少
    + 兼容性高
+ 缺点
    + 添加空标签，不利于页面优化


### 四、万能清除法 -- 给父元素添加伪类
```HTML
<style>
    .parent {
        background: blue;
    }
    .parent:after {
        content: "";
        clear: both;
        display: block;
        height: 0;
    }
    .child {
        height:200px;
        width: 50px;
        background: red;
        float: right;
    }
</style>

<div class="parent">
    <div class="child"></div>
</div>
```
[在线预览](https://codepen.io/ptcp3/pen/gOvMRRa)
+ 优点
    + 写法固定
    + 兼容性高

+ 缺点
    + 代码多



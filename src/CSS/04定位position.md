# 定位position

## position: relative
+ **不会移出正常文档流**
+ 相对于`自身位置`进行定位，占据定位前的位置，在不改变布局的情况下调整元素位置（因此会在此元素未添加定位时所在位置留下空白）

[查看示例](https://codepen.io/ptcp3/pen/mdXrVOO)

**tips**  
`position:relative` 对 table-*-group, table-row, table-column, table-cell, table-caption 元素无效。

## position: absolute
+ **元素会移出正常文档流**
+ 元素之前的位置被下面的元素顶替
+ 相对于`最近的非 static 祖先元素`来确定位置
+ 绝对定位的元素可以设置外边距（margins），且不会与其他边距合并
+ 没有设置宽度，元素的宽度由内容决定

[查看示例](https://codepen.io/ptcp3/pen/PoQGZNp)

## position: fixed
+ **元素会移出正常文档流**
+ 元素之前的位置被下面的元素顶替
+ 相对于`浏览器窗口`进行定位，即使窗口滚动元素也不移动

[查看示例](https://codepen.io/ptcp3/pen/QWQKyOx)

## position: sticky
+ **不会移出正常文档流**
+ 先根据正常文档流进行定位，然后相对最近的滚动祖先和containing block（最近的块级祖先）定位
+ 需要配合top使用

**注意**  
一个sticky元素会“固定”在离它最近的一个拥有“滚动机制”的祖先上

[查看示例](https://codepen.io/ptcp3/pen/LYQRGjV)

## position: static
+ 元素使用正常布局行为
+ top、right、bottom、left、z-index无效
+ 元素的默认定位值

## relative 和 absolute 分别是相对于谁进行定位的？
+ `relative`相对于自身位置进行定位
+ `absolute`相对于最近的非static祖先元素进行定位
+ `fixed`相对于浏览器窗口进行定位


`relative / sticky`会引起`重绘`  
`absolute / fixed`会引起`重构`


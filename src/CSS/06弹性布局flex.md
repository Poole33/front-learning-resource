# 弹性布局flex
> 我觉得学弹性布局，只要多玩几遍下面这个游戏就够了
> [flexboxfroggy](https://flexboxfroggy.com/#zh-cn)

弹性布局首先要设置 `display: flex;`

## justify-content
> 这个属性可以水平对齐元素

+ flex-start 元素和容器左对齐
+ flex-end 元素和容器右对齐
+ center 元素在容器内水平居中
+ space-between 元素之间保持相等距离
+ space-around 元素周围保持相等距离

[示例](https://codepen.io/ptcp3/pen/zYRNgWL)

## align-items
> 这个属性可以纵向对齐元素

+ flex-start 元素和容器顶部对齐
+ flex-end 元素和容器底部对齐
+ center 元素在容器内纵向居中
+ baseline 元素在容器基线位置显示
+ stretch 元素没有高度，拉伸到可以填满整个容器

[示例](https://codepen.io/ptcp3/pen/eYVgqKr)

## flex-direction
> 这个CSS属性定义了元素在容器里摆放的方向
    
+ row （默认值）元素摆放方向和文字方向一致（从左到右）
+ row-reverse 元素摆放方向和文字方向相反（镜像翻转）（从右到左）
+ column 元素从上到下摆放
+ column-reverse 元素从下到上摆放

[示例](https://codepen.io/ptcp3/pen/RwQKXqP)

## order
> 设置单个元素的横向位置，值为数字，默认值为0，元素按数字从小到大排序

[示例](https://codepen.io/ptcp3/pen/RwQKXdW)

## align-self
> 控制单个元素的纵向位置，属性值和align-items一致

[示例](https://codepen.io/ptcp3/pen/abqpeMX)

## flex-warp
> 元素是否换行

+ nowrap 所有元素一行显示
+ wrap 元素自动换行
+ wrap-reverse 元素自动换成逆序多行

[示例](https://codepen.io/ptcp3/pen/yLvMBBj)

## flex-flow
> flex-direction 和 flex-wrap 两个属性的缩写

[示例](https://codepen.io/ptcp3/pen/RwQpbWR)

## align-content
> 决定元素每行之间的间隔多远

+ flex-start 多行集中在顶部
+ flex-end 多行集中在底部
+ center 多行居中
+ space-between 每行之间保持相等距离
+ space-around 每行周围保持相等距离
+ stretch 每一行都被拉伸保证填满容器

[示例](https://codepen.io/ptcp3/pen/poaezoV)  
  
  
**拓展**：
[flex: 1 代表什么意思？](https://developer.mozilla.org/zh-CN/docs/Web/CSS/flex)
    


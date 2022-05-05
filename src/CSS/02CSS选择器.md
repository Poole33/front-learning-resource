# CSS选择器
> `CSS选择器`是一种规则，通过这种规则来找到你要添加样式的`dom元素`


## CSS2 选择器
    
### 元素选择器
```CSS
p { 
  // 给所有p标签添加样式
}
```

### id选择器
```CSS
#smile { 
  // 给ID为smile的元素添加样式
}
```

### 类选择器 
```CSS
.smile { 
  // 给所有类为smile的元素添加样式
}
```

### 群组选择器
```CSS
p,div,.red,.blue { 
  // 给元素p、div和类red、blue添加样式
}

.a.b {
  // 选择同时包含类ab的元素
} 

.a .b {
  // 选择.a内所有.b元素
}
```
        

### 通配符选择器
```CSS
* { 
  // 给所有元素添加样式
}
```
    
    
### 属性选择器
```CSS
[name] {
  // 选择带有name属性的元素
}

[name=xxx] {
  // 选择属性name等于xxx的元素
} 
        
[name~=xxx] {
  // 选择属性name包含xxx完整单词的元素
}

[name|=xxx] {
  // 选择属性name以xxx开头的元素
}

[name^=xxx] {
  // 选择属性name以xxx开头的元素，和上面的没区别
}

[name$=xxx] {
  // 选择属性name以xxx结尾的元素
}

[name*=xxx] {
  // 选择属性name包含xxx的元素
}
```

### 后代选择器
```CSS
a b {
  // 选择a内所有b元素
}

a>b {
  // 选择a内所有的子元素为b的元素   
}

a+b {
  // 选择紧接在a元素之后的b元素
}

a~b {
  // 选择前面有a元素的所有b元素
}
```

### 伪类选择器
```CSS
a:link {
  // a元素没点击前的样式
}

a:visited {
  // a元素选中后的样式
}

a:hover {
  // 鼠标悬停在a元素上时的样式
}

a:active {
  // 鼠标按下时a元素的样式
}
```


## CSS3 选择器

### 结构伪类选择器
```CSS
div:nth-child(n) {
  // 选择第n个子元素
}

p:only-of-type {
  // 选择子元素中只包含一个p的那个p
}

:root {
  // 选择根节点即html节点
} 

:empty {
  // 选择内部为空的元素
} 
```

### 目标伪类选择器
```CSS
:enabled {
  // 可以使用的元素
} 

:disabled {
  // 不可以使用的元素
} 

:checked {
  // 选中的元素
} 
```

### 伪元素选择器
> ::符号是用来区分伪类和伪元素的。支持CSS3的浏览器同时也都支持CSS2中引入的表示法:after

```CSS
::section {
  // 选中的文本 复制时的选中
} 

::after {
  // 元素后插入的内容
}

::before {
  // 元素前插入的内容
}

::first-letter {
  // 元素首字母
}

::first-line {
  // 元素首行
}
```
**伪元素和伪类的区别是什么？**
+ `伪类`是已有元素处于某种状态时添加对应的样式
+ `伪元素`是用于创建一些不在dom树中的元素，并为其添加样式



## 选择器优先级

+ `内联样式` 1000
+ `ID选择器` 100
+ `属性选择器` / `伪类选择器` / `类选择器` 10
+ `伪元素选择器` / `元素选择器` 1
+ `继承样式` 0

<!-- ## CSS继承
    继承特性主要是文本方面
    所有元素都可继承：visibility / cursor
    块级元素可继承：text-indent / text-align
    列表元素可继承：list-style / list-style-type / list-style-position / list-style-image
    内联元素（行内元素）可继承：letter-spacing / word-spacing / line-height / color / font / font-family / font-size / font-style / font-variant / font-weight / text-decoration / text-transform / direction -->


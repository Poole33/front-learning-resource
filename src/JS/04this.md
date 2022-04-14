# this

## this到底指向谁？

`this`是在`运行时`进行绑定的，它指向谁取决于`函数调用时`的各种条件。  
与`函数的位置`无关，只取决于`函数的调用方式`。

    当函数被调用时，会创建一个执行上下文。
    包含：函数在哪调用、函数的调用方法、传入的参数等信息。
    this就是记录的其中一个属性。

所以到底指向谁呢？继续往下看吧

## this有几种绑定规则？
> `this`有`四种绑定规则`。  
> 分别为：`默认绑定`、`隐式绑定`、`显示绑定`、`new 绑定`。  
> 优先级：`new 绑定` > `显示绑定` > `隐式绑定` > `默认绑定`


看到一段代码，想确认`this`指向哪时：
  1. 找到调用位置
  2. 判断使用下面四个规则的哪一条
        
### 默认绑定
> 当使用`独立函数调用`就应用`默认绑定`（不带任何修饰符的函数引用）。  
> `默认绑定`时，`this`绑定到`全局对象`。  
> 无法应用其它规则时，使用`默认绑定`。

``` javascript
// 形式1
var num1 = 1;

function fn1() {
  console.log(this.num1);
}

// 这就是默认绑定，直接调用，不带修饰符
fn1(); // 1
```

``` javascript
// 形式2
var num1 = 1;

function fn1() {
  console.log(this.num1);
}

function fn2() {
  var num1 = 2;
  fn1(); // 这里 fn1 依然指向全局对象，因为是直接调用，应用默认绑定规则
}

fn2(); // 1
```
    tips:
    严格模式，默认绑定 无法使用 全局对象。
    决定this绑定对象的并不是 调用位置 是否处于严格模式，而是函数体是否处于严格模式


### 隐式绑定
> 一句话概括就是：当链式调用时，就会应用`隐式绑定`。  
<!-- > 例如：`obj.fn1()`，`fn`中的`this`指向`obj`。 -->
<!-- > 调用位置有上下文对象，或者说是否被某个对象拥有或者包含 -->
只有最靠近的会影响`this`指向，例：
```javascript
var obj = {
  num1: 1,
  fn: function() {
    console.log(this.num1);
  },
  pro: {
    num1: 2,
    fn: function() {
      console.log(this.num1);
    }
  }
}

// this指向obj
obj.fn(); // 1

// this指向pro
obj.pro.fn(); // 2
```

#### 隐式丢失
> 当把`链式调用的函数`，赋值给一个`变量`时，就会产生`隐式丢失`。
```javascript
// 隐式丢失1
var num1 = 1;

function fn1() {
  console.log(this.num1);
}

var obj = {
  num1: 2,
  fn1: fn1
}

var fn2 = obj.fn1;

fn2(); // 1

/**
 *原因：
 *  
 * fn2是obj.fn1的引用，但实际引用的是fn1本身
 * 
 * 此时 fn2 是没有任何修饰符的调用，因此应用默认绑定
**/

```
```javascript
// 隐式丢失 2
var num1 = 1;

var obj = {
  num1: 2,
  fn1: function() {
    console.log(this.num1);
  }
}

function doFn(fn) { 
  fn(); 
}

doFn(obj.fn1); // 1

/**
 *原因：
 *  
 * 参数传递是一种隐式赋值
 * 
 * 其它原因，同隐式丢失1
 * 
**/
```


### 显示绑定
> 把调用函数的`this`指向，**强制绑定**在某个对象上，被成为`显示绑定`。

**显示绑定的方法：**`.call(...)`、`.apply(...)`

```javascript
// 显示绑定
var num1 = 1;

var obj = {
  num1: 2
}

function fn1() {
  console.log(this.num1);
}

fn1.call(obj); // 2
```

`call` 和 `apply` 的区别：

    相同点：
      1. 第一个参数都是一个对象。（即想要设置的this指向）
      2. 都会直接执行函数
    不同点：
      .call(...) 第一个参数之后的参数，会直接作为被绑定函数的传参
      .apply(...) 第二个参数是数组，会分解后作为绑定函数的传参

```javascript
// call 和 apply 的不同点
function fn1(n1, n2) {
    console.log(n1, n2);
}

fn1.call(null, 1, 2); // 3
fn1.apply(null, [1, 2]); // 3
```

#### 硬绑定 .bind()
> 效果和上面一样，但`bind`会返回一个硬编码的`新函数`。


`硬绑定`解决了[`隐式丢失`](#隐式丢失)的问题。

``` javascript
var num1 = 1;

var obj = {
  num1: 2
}

function fn1() {
  console.log(this.num1)
}

var fn2 = fn1.bind(obj); // 没有产生隐式丢失

foo(); // 2
```

用`.call()`、`.apply()`模拟硬绑定。

```javascript
// 已经显示绑定过的函数，无法再显示绑定
var num1 = 1;

var obj = {
  num1: 2
}

function fn1() {
  console.log(this.num1);
}

var fn2 = function() {  // 解决了前面赋值操作造成的 隐式丢失 的问题
  fn1.call(obj);
}

fn2.call(window); // 2
```
现在很多函数支持，传递参数来把参数绑定到了`this`，原理就是通过`.call()`和`.apply()`实现显示绑定，可以少写一些代码。例如：
```javascript
[].forEach(fn, obj); // obj会显示绑定到this
```

>如果把`null`或者`undefined`作为`this`的绑定参数传入`.call()`、`.apply()`、`.bind()`，这些值调用时会被忽略，并应用`默认绑定`规则


### new 绑定
> 在对一个函数使用`new`来调用，来决定`this`的指向，称为`new绑定`。

<!-- 没有构造函数，只有对函数的构造调用 -->
想要知道`new绑定`，`this`到底指向哪，就需要先弄清楚，`在new 一个函数时发生了什么？`

1. 创建一个`新对象`
2. `新对象`的`[[ prototype ]]`连接到函数的`prototype`（这一步可以先忽略，到原型链一章讲解）
3. 然后确认函数的返回值
   + 如果函数return不是一个对象， 把`this`指向创建的`新对象`，然后执行构造函数（使用`this.`赋值的属性会放进新对象）
   + 如果函数`return`一个对象，`new`调用的函数调用会返回这个对象，并**丢弃**之前创建的`新对象`
4. 结束

```javascript
// 函数返回不是对象
var num1 = 1;

function Smile() {
  this.num1 = 2;
}

var obj = new Smile();

console.log(obj.num1); // 2
console.log(num1); // 1
```

```javascript
// 函数返回的是对象
var num1 = 1;

function Smile() {
  this.num1 = 2;
  this.num2 = 0;
  return {
    num1: 3
  }
}

var obj = new Smile();

console.log(obj.num1); // 3
console.log(obj.num2); // undefined
console.log(num1); // 1
```


### 特殊情况 - 箭头函数
> `箭头函数`不使用`this`绑定的四种规则，而是根据`外层作用域`来决定`this`的指向

调用函数时，外部的`this`改变，箭头函数的`this`跟着改变。
```javascript
var num1 = 1;

var obj = {
  num1: 2
}

function fn1() {
  var num1 = 3;
  // 箭头函数的this指向 与fn1的this指向 一致
  setTimeout(() => {
    console.log(this.num1);
  }, 0)
}

fn1(); // 1

fn1.call(obj); // 2
```
  
`箭头函数`无法使用`.bind()`绑定。  
  
`箭头函数`混淆了`this`绑定规则和词法作用域规则，所以他和正常的`this`机制是两种写作风格，我们平时工作中，应该只使用其中一种风格，如果避免混淆使用，会让代码可读性降低，增加维护成本。






# Promise
讲到`Promise`就不得不比较一下`回调`和`Promise`的区别和关系。  
`JS`实现`异步`最常用的方式就是使用`回调`，`Promise`是`ES6`中才引入的概念，他主要是为了解决`回调`存在的一些问题，可以看作`回调`的升级版。  
那`回调`中存在什么问题，和`Promise`又有什么区别呢？

## 回调的问题

### 1. 信任问题
  假设我去奶茶店买`珍珠奶茶`，我觉得珍珠不够多，想加一份珍珠。于是我跟店员说我加一份珍珠（`回调`，在奶茶做好后执行），然后服务员帮我做好标记，我付好钱之后，就等着开心的喝奶茶了。大部分情况下，我是可以愉快的获得一份加了珍珠的珍珠奶茶的，但有时也会有忘记加的情况（别问我为什么知道，怨念..），这就是`回调`可能存在的信任问题，我们不能确定你的`回调函数`会如何执行，执行几次，以及会不会执行。  
  ```javascript
  // 伪代码
  function buyMilkTea() {
    // 制作奶茶，接收一个加料回调
    doMilkTea(addSubject);

    // 加料
    addSubject() {
      // 加一份珍珠
    }
  }
  ```

  **`Promise`是如何解决信任问题的呢？**
  `Promise`和`回调`不同，他是通过分离回调（一个成功通知，一个错误通知），告诉我他完成了（只会告诉一次），接下来要执行什么，我自己来决定（决定权放在我这）。  
  还是买奶茶的例子，我付好钱，等待叫号。会有两种结果：
  1. 叫到我的号，奶茶做好了（成功通知）。这时我可以再买一份珍珠自己加（得到通知后，自己决定后面干嘛）
  2. 叫到我的号，但是因为原料不足，我的奶茶做不出来（失败通知）。这时我可以选择换一个品类，或者退钱

在`回调`中，上面的两种情况是写在一个回调函数中的，这样分开写成功和失败的逻辑，也让代码更清晰。

  ```javascript
  // 伪代码
  new Promise((resolve, reject) => {
    buyMilkTea();
    resolve(); // 通知叫号
  }).then(
    res => { // 成功回调
      addSubject(); // 买一份珍珠，自己加
    },
    error => { // 失败回调
      // 退钱
    }
  )
  ```


### 2. 回调地狱
`回调`是异步执行的，不能保证回调函数的`执行顺序`。因此为了保证回调函数的执行顺序，就会使用回调中嵌套回调一层层嵌套下去，实现我们想要的执行顺序。想想这种代码就非常的头疼...
```javascript
// 伪代码，大概模拟下回调地狱的情况。。。
getUp(function() {
  brushTeeth(function() {
    haveBreakfast(function() {
      goToSchool(function() {
        haveLunch()
        goHome(function() {
          sleep()
        })
      })
    })
    makeUp()
    lookInTheMirror()
  })
})
```

  **`Promise`是如何解决回调地狱的呢？**
  和信任问题一样，通过只返回一次状态（成功 / 失败），告诉我他做完，接下来要做什么由我来决定。而且Promise的.then中返回会自动创建一个新的Promise来进行链式链接
  ```javascript
  // 这种形式更符合我们大脑的逻辑
  new Promise((resolve,reject) => {
    getUp();
    resolve();
  }).then(res => {
    brushTeeth();
    haveBreakfast();
    makeUp();
    lookInTheMirror();
    return;
  }).then(res => {
    goToSchool();
    return;
  }).then(res => {
    haveLunch();
    return;
  }).then(res => {
    goHome();
    return;
  }).then(res => {
    sleep();
    return;
  });
  ```

**总结**：`回调`是把方法交给别人去执行，`Promise`是别人告诉你“我执行完了”，然后自己控制下面执行什么
## Promise详解
> 通过上述的讲解，我们也大致了解了Promise解决的问题和为什么要有Promise。所以Promise就像一个法官，对我们执行的内容进行判决（只会判决一次，不能改判），我们得到判决结果（then中获得），对响应的结果进行处理。我们也相当于一个法官，返回一个结果（又一次判决），下面通过.then还能获得我们的判决结果（实现链式调用）

### Promise API
#### Promise 构造函数
+ 必须和`new`一起使用
+ 必须提供一个函数回调
+ 这个函数接受两个函数回调`resolve`和`reject`
    + `reject`拒绝这个`Promise`
    + `resolve`既可以完成也可以拒绝

```javascript
new Promise((resolve, reject) => { // 提供一个函数回调，这个函数有两个回调resolve和reject
  // ... 做些事，这里的内容是同步的
  
  // 完成判决
  resolve();
  
  // 拒绝判决 两种方式
  // resolve(Promise.reject());
  // reject();
});
```

#### then( )和catch( )
> 每个`Promise`实例都有`then()`和`catch()`方法，可以通过这两个方法来处理Promise的判决结果（都是微任务，插入任务队列）。
+ `then()`
  有两个参数，第一个是处理完成结果的回调，第二个是处理错误信息的回调
  ```javascript
  new Promise((resolve, reject) => {
    resolve(1);
    // 或者
    // reject(2);
  }).then(
    res => {}, // 第一个参数，完成结果的回调
    error => {} // 第二个参数，处理错误信息的回调
  )
  ```
+ `catch()`
只有一个参数，处理错误信息的回调。等价于`then(null, rejected)`
  ```javascript
    new Promise((resolve, reject) => {
      // resolve(1);
      // 或者
      reject(2);
    }).catch(
      error => {} // 处理错误信息的回调
    )
    ```

#### Promise.resolve( )和Promise.reject( )
+ `Promise.reject( )` 快速创建`已被拒绝`的Promise
  ```javascript
  Promise.reject('error')
  .then(
  (res) => {
    // 不会执行
  },
  error => {
    // 执行
    console.log(error); // `error`
  });
  ```
+ `Promise.resolve( )` 快速创建一个`已完成/拒绝`的Promise
  <!-- + 如果传入值是thenable，会展开thenable值 -->
  + 传入的是个数据，创建一个已完成的Promise，并返回传入的数据
  + 如果传入值是含有then()的对象，会获取then()返回的值，并创建Promise返回这个值
  + 传入的是真正的Promise，直接返回这个promise，不会有任何开销

  ```javascript
    // 传入的是数据
    Promise.resolve('fulfilled')
    .then(
    (res) => {
      // 执行
      console.log(res); // `fulfilled`
    },
    error => {
      // 不会执行
    });


    // 传入的是thenable
    var obj = {
      then(callBack) {
        callBack('33');
      }
    }

    Promise.resolve(obj)
    .then(
    (res) => {
      console.log(res); // `33`
    });



    // 传入Promise
    var p1 = new Promise((resolve, reject) => {
      resolve('33');
    })

    Promise.resolve(p1) // 原封不动返回p1
    .then(res => {
      console.log(res); // `33`
    })
    ```

通过`Promise.resolve()`过滤来获得可信任性的`值`/`Promise`，完全没有坏处



### Promise的特点
+ `then()`的回调是微任务（插入任务队列，进行异步调用）
+ 链式`then()`会注册很多微任务，然后`顺序调用`
+ `then()`会自动创建并返回一个新`Promise`
+ `then()`中`return`一个`Promise`会替换`then()`自动生成的`Promise`
+ 两个`独立的Promise`的`回调`运行顺序无法预测
+ 每个`Promise`内只能判决一次（保证回调只会执行一次），重复的判决会被抛弃
+ `Promise`只有一个判决值（`resolve()` / `reject()`）
+ `Promise`的**返回方法只有一个参数**（`resolve()` / `reject()` 只接受一个参数）
+ `Promise`出现错误，但没有设置处理函数，那么这个错误会顺着`then()`的链条传播下去，直到这个错误被处理

### Promise模式
#### Promise.all([ ])
> 数组内的任务全部完成才完成。返回一个`Promise`，返回值是一个数组。  
> 有一个被拒绝，`Promise.all([ ])`就会立即被拒绝，并丢弃其它所有`Promise`的结果。

```javascript
// 全部成功
var p1 = Promise.resolve('33');
var p2 = Promise.resolve('66');

Promise.all([p1, p2])
.then(res => {
  console.log(res); // ['33', '66']
});
```
```javascript
// 有失败成功
var p1 = Promise.resolve('33');
var p2 = Promise.reject('err');

Promise.all([p1, p2])
.then(res => {
  console.log('then'); // 不会执行
})
.catch(err => {
  console.log(err); // 'err'
});
```

#### Promise.race([ ])
> 众多`Promise`赛跑，只有第一个跨过终点线的`Promise`被响应，其它抛弃（会把数组中所有的`Promise`都执行）。一旦有一个`Promise`决议为拒绝，就会拒绝。  

**注意**：
+ 至少有一个参赛者，传入空数组，`Promise`永远处于pending状态。（永远不要给空数组）
+ 由多个`Promise`、`thenable`、`立即值`组成。立即值在竞争中无意义，因为显然第一个获胜。

```javascript
// 有立即值的竞赛
var p1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(1);
  }, 1000);
});

var p2 = 2;

Promise.race([p1, p2])
.then((res) => {
  console.log(res); // 2
});
```

```javascript
// 传入空数组
Promise.race([])
.then((res) => {
  console.log(res); // 不会执行
}); // Promise {<pending>}
```

```javascript
// 有拒绝的竞赛
var p1 = Promise.resolve(1);
var p2 = 2;
var p3 = Promise.reject(3);

Promise.all([p1, p2, p3])
.then(res => {
  console.log('then'); // 不会执行
})
.catch(err => {
  console.log(err); // 'err'
});
```

#### Promise.any([ ])
> 只需完成一个（忽略拒绝，不需要数组中的Promise全部执行）。注意和race的区别

<!-- #### 其它
+ `Promise.first([ ])`
  第一个完成的Promise（不需要数组中的Promise全部执行），忽略后续的任何拒绝和完成
+ `Promise.last([ ])`
  最后一个promise完成，忽略前面的任何拒绝和完成 -->



### Promise相关的面试题

#### 手写实现一个Promise.all()
```javascript
function promiseAll(arr) {
  if (arr.length == 0) return;
  let res = [];
  let idx = 0; // 保证最后一个promise完成后进行返回
  return new Promise((resolve, reject) => {
    arr.forEach((pro, i) => {
      pro.then(val => {
        idx++;
        res[i] = val;  // 保证结果值与传入的promise的顺序一致，确保promise中有异步的情况下也能正常返回
        if (idx == arr.length) resolve(res);
      }, err => {
        reject(err); // 有一个拒绝，就直接拒绝
      })
    });
  })
}

// 测试
var p1 = Promise.resolve(1);
var p2 = Promise.resolve(2);
promiseAll([p1, p2])
.then(res => {
  console.log(res); // [1, 2]
});
```

#### 手写实现一个Promise.race()
```javascript
function promiseRace(arr) {
  // 没有传值报错
  if (!arr) throw(TypeError('undefined is not iterable'));
  // 传入的是空数组，一直保持pending
  if (arr.length == 0) return new Promise((resolve, reject) => {});
  // 正常情况
  return new Promise((resolve, reject) => {
    arr.forEach(p => {
      // 运用Promise.resolve()的特性，解析一下p，保证返回promise
      Promise.resolve(p).then(res => {
        resolve(res);
      }, err => {
        reject(err);
      });
    })
  });
}

// 测试
var p1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(1);
  }, 1000);
});

var p2 = 2;

promiseRace([p1, p2])
.then((res) => {
  console.log(res); // 2
});
```


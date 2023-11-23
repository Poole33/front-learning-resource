# nextTick的实现原理

在了解一个知识点的原理之前，先了解清楚他解决了什么问题，可能更加能加深我们对这个知识点的影响。  

那 nextTick 解决了什么问题呢？我们为什么要用 nextTick 呢？  

首先，vue 为了防止每修改一个数据，就对页面进行重新渲染，而采用了异步更新 DOM。每当我们修改一个数据，会先把对应的 watcher 放入一个队列当中，在下一个事件循环周期执行。  

而在我们修改一个数据，到下一个事件循环周期前，我们无法获取到更新后的DOM节点，因为渲染是异步的，而我们获取 DOM 操作是本次事件循环的同步事件。  

**为了解决这个问题，nextTick 来了。**  

## 原理
在我们执行 this.$nextTick(callback) 时，内部会把 callback 是用 Promise 包裹，放入 nextTick 队列，在下一个事件循环周期执行（与watcher队列同一个周期），到了执行 nextTick 的事件循环周期时，会把 callback 再次加入微任务队列，因为此次事件周期会进行 DOM 更新操作，那么下一个周期执行 callback 就一定在 DOM 更新之后了。

**注意：**  
包裹 callbak 的异步方式会有几种情况，根据浏览器的支持情况： Promise > setImmediate > setTimeout > MutationObserver

这就是 nextTick 的由来了，如果你恰好还了解 vue 的生命周期，你会发现 updated 生命周期钩子也是在 DOM 更新完成之后调用的，那么 updated 和 nextTick 有什么区别呢？能互相替换嘛？

## updated 和 nextTick 的区别 

下面引用官网对 updated 生命周期的解释。
```
父组件的更新钩子将在其子组件的更新钩子之后调用。

这个钩子会在组件的任意 DOM 更新后被调用，这些更新可能是由不同的状态变更导致的，因为多个状态变更可以在同一个渲染周期中批量执行（考虑到性能因素）。如果你需要在某个特定的状态更改后访问更新后的 DOM，请使用 nextTick() 作为替代。

这个钩子在服务器端渲染期间不会被调用。
```

但是经过我的测试，在同一个同步代码周期内更改的数据，在 nextTick 和 updated 中获取的值是一致的。

```javascript
let test = ref('before')

onMounted(() => {
    test.value = 'after'
    
    nextTick(() => {
        console.log('nextTick', test.value, document.querySelector('.box2').innerHTML)  // nextTick 111 111
    })
    
    test.value = '111'
})

onUpdated(() => {
    console.log('updated', test.value, document.querySelector('.box2').innerHTML)  // updated 111 111
})
```

但 nextTick 和 updated 有一个不同，nextTick 返回的是一个异步函数，而 updated 只是一个生命周期钩子，没有返回值。这样就会在使用中有下面的区别。  

```javascript
let test = ref('before')

onMounted(async () => {
    test.value = 'after'
    
    await nextTick()
    console.log('nextTick', test.value, document.querySelector('.box2').innerHTML)  // nextTick 111 after
    
    test.value = '111'
})

onUpdated(() => {
    console.log('updated', test.value, document.querySelector('.box2').innerHTML)  // updated 111 111
})
```

这样我们就可以获取在某个特定的状态更改后的 DOM 了。

以上就是我发现的 nextTick 和 updated 的区别了，其实在大部分情况下，他们两个是可以互相替代的，由于现在 vue3 的生命周期 也支持 hooks 的写法了，在代码可读性方面两个的差别也不大，所以具体使用那种，一是看上面的区别，再就是看公司的规范了。
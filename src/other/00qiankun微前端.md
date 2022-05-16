# qiankun微前端
**本文参考**：  
[官网](https://qiankun.umijs.org/zh/guide)  
[你可能并不需要微前端](https://zhuanlan.zhihu.com/p/391248835)
## 什么是微前端？
Techniques, strategies and recipes for building a modern web app with multiple teams that can ship features independently. -- [Micro Frontends](https://micro-frontends.org/)
    微前端是一种多个团队通过独立发布功能的方式来共同构建现代化 web 应用的技术手段及方法策略。

## qiankun是怎么来的？
> 所有的技术都是为了解决当前的现实问题，然后通过思考和实践创造出来的。微前端本质上是为了解决组织和团队间协作带来的沟通和管理的问题

引用微前端作者的思想：
> 微前端是康威定律在前端架构上的映射。
> 康威定律指导思想：既然沟通是大问题，那么就不要沟通就好了

作者认为大型系统都逃不过熵增定律，宇宙的本质，所有的东西都会从有序走向无序。一个东西如果你不去管理，他就会变成一坨垃圾，所以你想要维持一个东西的有序性，就要付出努力去维护他。所以从中找到平衡，qiankun就诞生了。通过分治的手段，**让上帝的归上帝，凯撒的归凯撒**

## 什么情况下使用qiankun？

**我们在开发中可能会碰到下面的问题**：
+ 旧的系统不能下，新的需求还在来
+ 公司内部有很多的系统，不同系统间可能需要展示同一个页面
+ 一个系统过于庞大，每个人分别管理一个模块，git分支比较混乱。想要把系统拆分开来

**微前端首先解决的，是如何解构巨石应用**
> 核心价值：技术栈无关，应用之间不应该有任何直接或间接的技术栈、依赖、以及实现上的耦合。


**作者认为正确的微前端方案的目标应该是**：
> 方案上跟使用 iframe 做微前端一样简单，同时又解决了 iframe 带来的各种体验上的问题


## qiankun的原理
> qiankun 是一个基于 [single-spa](https://github.com/single-spa/single-spa) 的[微前端](https://micro-frontends.org/)实现库，旨在帮助大家能更简单、无痛的构建一个生产可用微前端架构系统。

由`qiankun`框架内部`fetch`请求资源，解析出js、css文件和HTML document，插入到主应用指定的容器中（使用HTML Entry接入方式）

1. 调用`import-html-entry`模块的`importEntry`函数，获取到对应子应用的html文件、可执行脚本文件以及publicpath
2. 调用`getDefaultTplWrapper`将子应用的html内容用div标签包裹起来
3. 调用`createElement`函数生成剔除html、body、head标签后的子应用html内容（通过innerHTML达到过滤效果）
4. 调用`getRender`函数得到render函数(所以子应用一定要有render函数)
5. 调用第4步得到的`render`，将container内部清空，并将子应用的dom元素渲染到指定的contanter元素上
6. 调用`getAppWrapperGetter`函数，生成一个可以获取处理过的子应用dom元素的函数`initialAppWrapperGetter`，以备后续使用子应用dom元素
7. 如果sandbox为true，则调用`createSandboxContainer`函数
8. 执行`execScripts`函数，执行子应用脚本
9. 执行`getMicroAppStateActions`函数，获取onGlobalStateChange、setGlobalState、offGlobalStateChange，用于主子应用传递信息
10. 执行`parcelConfigGetter`函数，包装mount和unmount  

[上述步骤的源码](https://github.com/umijs/qiankun/blob/b8250db8b6946eb28bbb1fe0b84b09af28e7cbae/src/loader.ts#L244)

## qiankun如何实现隔离？
    
### 沙箱隔离
> qiankun的沙箱有2种 JS沙箱 和 CSS沙箱
#### JS沙箱
> JS沙箱又分为2种，快照沙箱（为了兼容IE）和 代理沙箱
##### 快照沙箱 snapshotSandbox
> 基于`diff`实现，用来兼容不支持`Proxy`的浏览器，只适用单个子应用。会污染全局`window`

1. **激活沙箱**：将主应用window的信息存到`windowSnapshot`里
2. 根据`modifyPropMap`，恢复为子应用的window信息
    > 读取和修改的是window中的数据，`windowSnapshot`是缓存的数据
3. **退出沙箱**：根据`windowSnapshot`把window恢复为主应用数据，将`windowSnapshot`和window进行diff，将变更的值存到`modifyPropMap`中，然后把window恢复为主应用数据

**总结**：
+ `windowSnapshot`存`主应用`的window信息
+ `modifyPropMap`存`子应用`修改的window信息

[相对应的源码](https://github.com/umijs/qiankun/blob/b8250db8b6946eb28bbb1fe0b84b09af28e7cbae/src/sandbox/snapshotSandbox.ts)

##### 代理沙箱
> 代理沙箱也分为2种，单例和多例，都是由Proxy实现
###### 单例沙箱 legacySandbox
> 为了兼容性 singular 模式下依旧使用该沙箱，等新沙箱稳定之后再切换。    创建 addedPropsMapInSandbox（沙箱期间新增的全局变量）、modifiedPropsOriginalValueMapInSandbox（沙箱期间更新的全局变量）、currentUpdatedPropsValueMap（持续记录更新的(新增和修改的)全局变量的 map 用于在任意时刻做 snapshot） 三个变量，**前两个用来恢复主应用window，最后一个用来恢复子应用window**。同样会污染window，但性能比快照沙箱稍好，不用遍历window

1. **激活沙箱**：根据`currentUpdatedPropsValueMap`还原子应用的window数据
2. window只要变动，在`currentUpdatedPropsValueMap`中进行记录
   1. 判断`addedPropsMapInSandbox`中是否有对应 key 的记录，没有新增一条，有的话往下执行
   2. 判断`modifiedPropsOriginalValueMapInSandbox`中是否有对应 key 的记录，没有的话，记录从window中对应key/value，有的话继续往下执行
   3. 修改window对应的key/value
3. **退出沙箱**：根据`addedPropsMapInSandbox`和`modifiedPropsOriginalValueMapInSandbox`还原主应用的window信息  

[相对性的源码](https://github.com/umijs/qiankun/blob/b8250db8b6946eb28bbb1fe0b84b09af28e7cbae/src/sandbox/legacy/sandbox.ts)

###### 多例沙箱 proxySandbox
> 主应用和子应用的window独立，不再共同维护一份window，终于JS沙箱也和qiankun微前端的思想统一了…实行了分治。不会污染全局window，支持多个子应用。

1. **激活沙箱**
2. 取值，先从自己命名空间下的`fakeWindow`找key，没找到，找window
3. 赋值，直接给自己命名空间下的`fakeWindow`赋值
4. **退出沙箱**  

[相对应的源码](https://github.com/umijs/qiankun/blob/b8250db8b6946eb28bbb1fe0b84b09af28e7cbae/src/sandbox/proxySandbox.ts)



#### CSS沙箱
> 严格沙箱 和 实验性沙箱
##### 严格沙箱
> 在加载子应用时，添加`strictStyleIsolation: true`属性，会将整个子应用放到`Shadow DOM`内进行嵌入，完全隔离了主子应用
                
![示例](https://tva1.sinaimg.cn/large/008i3skNly1gvtycrhx94j31do0hiaex.jpg)
                
**缺点**：子应用中应用的一些弹框组件会因为找不到body而丢失

##### 实验性沙箱
> 在加载子应用时，添加`experimentalStyleIsolation: true`属性，实现形式类似于vue中style标签中的scoped属性，qiankun会自动为子应用所有的样式增加后缀标签，如：div[data-qiankun=“xxx”]，这里的XXX为注册子应用时name的值

![示例](https://tva1.sinaimg.cn/large/008i3skNly1gvtyf8g22pj32740k47c7.jpg)

**缺点**：子应用中应用的一些弹框组件会因为插入到了主应用到body而丢失样式

[相对应的源码](https://github.com/umijs/qiankun/blob/b8250db8b6946eb28bbb1fe0b84b09af28e7cbae/src/sandbox/patchers/css.ts)
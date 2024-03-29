# v-if和v-for能否一起使用？

先说结论吧：在实际的开发中，他们是可以放在一起使用的，也不会报错。但官方并不建议你这么做，这是为什么呢？一起来看看吧。  

## 优先级

在 vue2 中，v-for 的优先级**大于** v-if。  
在 vue3 中，v-for 的优先级**小于** v-if。

仅仅因为优先级不同好像也没什么大不了的，为什么不让一起使用呢？

## 性能考虑

这就要考虑到我们会把 v-if 和 v-for 放在一起使用的实际场景了。分为以下两种情况：

1. 先根据 v-if 判断要不要展示盒子，在使用 v-for 在盒子里遍历出列表。
2. 在 v-for 的时候，通过使用 v-if 来过滤掉一些我们不想展示的数据。

上面两种情况：  

第 1 个，把 v-if 和 v-for 放在一行使用在 vue2 中是无法实现的，因为 v-for 的优先级高。vue3 可以实现。  
第 2 个，把 v-if 和 v-for 放在一行使用在 vue3 中是无法实现的，因为 v-if 的优先级高。vue2 中不行。

通过分析，好像只要判断好使用的场景，也是可以把他们放在一起使用的嘛，为什么官方不建议我们这么做呢？这就 牵扯到性能的问题了。  

首先第 1 种情况，因为不同版本的 vue 优先级逻辑不同，考虑到降低心智负担和代码的统一性的考虑，因此在代码风格方面，不建议放在一起使用。  

针对第 2 种情况，由于 v-for 的优先级**大于** v-if。vue 在执行时，会先把所有的数据都遍历一遍，然后再根据 v-if 判断，把不需要的数据给过滤掉。这样在计算 VNode 的过程中，就会有一部分其实是不需要在 v-for 阶段进行计算的，就会影响渲染的速度。所以遇到这种情况，使用 computed，首先对数据进行遍历过滤，然后在使用 v-for 进行渲染，就会节省了计算多余 VNode 的时间，从而提升性能。因此从系统性能方面，不建议放在一起使用。  

通过上面的分析，想必大家对于 v-if 和 v-for 能否一起使用，应该也有了自己的判断了～
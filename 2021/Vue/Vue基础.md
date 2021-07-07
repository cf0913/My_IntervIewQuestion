# Vue基础
#### computed和watch的区别
```js
（1）computed 是计算一个新的属性，并将该属性挂载到 Vue 实例上，而 watch 是监听已经存在且已挂载到 Vue 实例上的数据，所以用 watch 同样可以监听 computed 计算属性的变化。

（2）computed 本质是一个惰性求值的观察者，具有缓存性，只有当依赖变化后，第一次访问 computed 属性，才会计算新的值。而 watch 则是当数据发生变化便会调用执行函数。

（3）从使用场景上说，computed 适用一个数据被多个数据影响，而 watch 适用一个数据影响多个数据。
```
---
#### 虚拟DOM和Diff
```js
// Virtual DOM-------------------------
1. 可以简单描述为用一个js的对象来描述一个DOM
2. 例如一个div，可以是一个 div 对象，然后 TagName、props 等这些作为这个对象的属性
3. 子元素则可以是 children 属性的值，子元素的结构和父元素一致，也有 TagName、props 这些属性
4. 然后这样一个对象，就可以看作为一个 DOM 树的数值化
5. 当渲染的时候，就根据当前对象的属性值去渲染，例如 TagName 是 div 的则创建div，且遍历其 children 生成子元素
6. 最终挂载到页面
7. Virtual DOM 的这种对象化/数值化 DOM，可以很方便的 diff 和更新，更加的节省性能

// Diff算法-----------------------------
1. diff算法的本质是找出两个对象之间的差异，目的是尽可能复用节点
2. 由于 Virtual DOM 的存在，diff 就方便很多
3. 一个页面性能开销最大的就是渲染，diff 算法可以很好的避免重复和无意义的渲染
4. 比较新旧节点的时候，比较只会在同层级进行, 不会跨层级比较
  - 新老节点均有children子节点，则对子节点进行diff操作，调用 updateChildren
  - 如果老节点没有子节点而新节点存在子节点，先清空老节点DOM的文本内容，然后为当前DOM节点加入子节点
  - 当新节点没有子节点而老节点有子节点的时候，则移除该DOM节点的所有子节点。
  - 当新老节点都无子节点的时候，只是文本的替换
5. updateChildren 方法，diff 算法的核心
  - 子元素diff的时候，不是使用2个指针遍历所有新旧children，而是首尾各有1个指针，共4个指针。
  - 两两判断，优化在元素顺序变化的情况下，移动dom，而不是新建
  - 除一开始判断旧节点是否为空之外，主要有4个对比过程从头得知，新旧节点分别都有2个指针，分别指向各自的头部与尾部
    - 当新旧节点的俩头部相同(sameVnode为ture)，进入patchNode方法，同时各自的头部指针+1；
    - 当新旧节点的俩尾部相同，进入patchNode方法，同时各自的尾部指针-1；
    - 当oldStartVnode，newEndVnode相同，说明oldStartVnode已经跑到了后面，那么就将oldStartVnode.el移到oldEndVnode.el的后边。oldStartIdx+1，newEndIdx-1；
    - 当oldEndVnode，newStartVnode相同，说明oldEndVnode已经跑到了前面，那么就将oldEndVnode.el移到oldStartVnode.el的前边。oldEndIdx-1，newStartIdx+1；
    - 当以上4种对比都不成立时，通过newStartVnode.key 看是否能在oldVnode中找到，如果没有则新建节点，如果有则对比新旧节点中相同key的Node，newStartIdx+1
  - 当循环结束时，这时候会有两种情况。oldStartIdx > oldEndIdx，可以认为oldVnode对比完毕
  - 此时newStartIdx和newEndIdx之间的vnode是新增的，调用addVnodes，把他们全部插进before的后边
  - newStartIdx > newEndIdx，可以认为newVnode先遍历完，oldVnode还有节点。
  - 此时oldStartIdx和oldEndIdx之间的vnode在新的子节点里已经不存在了，调用removeVnodes将它们从dom里删除

// Vue的diff和React的diff
1. Vue的diff
  简单来说，就是vue的diff算法在对新老虚拟dam进行对比时，是从节点的两侧向中间对比；如果节点的key值与元素类型相同，属性值不同，就会认为是不同节点，就会删除重建
2. React的diff
  react的diff算法在对新老虚拟dom进行对比是，是从节点左侧开始对比，就好比将新老虚拟dom放入两个栈中，一对多依次对比；如果节点的key值与元素类型相同，属性值不同，react会认为是同类型节点，只是修改节点属性
3. 相同点
  都只进行同级比较，忽略了跨级操作；常见的现象就是对数组或者对象中的深层元素进行修改时，视图层监测不到其变化，故不会对dom进行更新，需调用一些特质方法来告诉视图层需要更新dom
```
---
#### 为什么vue的data必须是一个函数
```js
因为对象是引用类型的

如果data是一个对象，而不是函数，那么该对象可以被其他组件所修改

函数的话则没有这个问题，函数的化可以保证每个组件初始化的data都是独立的对象，有独立的内存空间
```
---
#### $nextTick原理
```js
// 把主线程执行一次的过程叫一个tick，所以nextTick就是下一个tick的意思
1. 为了节省性能，Vue更新DOM是异步的，所以数据更新后，并不是实时去更新页面
2. 只要侦听到数据变化，Vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据变更。如果同一个 watcher 被多次触发，只会被推入到队列中一次。这种在缓冲时去除重复数据对于避免不必要的计算和 DOM 操作是非常重要的
3. 然后，在下一个的事件循环“tick”中，Vue 刷新队列并执行实际 (已去重的) 工作
4. $nextTick 实现使用的是使用 微任务 Promise.then、MutationObserver 和 宏任务 setImmediate
5. 当这些方法都不兼容的时候，兜底方案是 宏任务 setTimeOut
6. 因为 宏任务 UIRender 是在宏任务的最后执行的，所以上述方法都是在 UI Render 之前执行的，保证了在下一次 tick 之前执行
```
---
#### 循环遍历的时候，key的作用
```js
vue 中 key 值的作用可以分为两种情况来考虑。

第一种情况是 v-if 中使用 key。由于 Vue 会尽可能高效地渲染元素，通常会复用已有元素而不是从头开始渲染。因此当我们使用 v-if 来实现元素切换的时候，如果切换前后含有相同类型的元素，那么这个元素就会被复用。如果是相同的 input 元素，那么切换前后用户的输入不会被清除掉，这样是不符合需求的。因此我们可以通过使用 key 来唯一的标识一个元素，这个情况下，使用 key 的元素不会被复用。这个时候 key 的作用是用来标识一个独立的元素。

第二种情况是 v-for 中使用 key。用 v-for 更新已渲染过的元素列表时，它默认使用“就地复用”的策略。如果数据项的顺序发生了改变，Vue 不会移动 DOM 元素来匹配数据项的顺序，而是简单复用此处的每个元素。因此通过为每个列表项提供一个 key 值，来以便 Vue 跟踪元素的身份，从而高效的实现复用。这个时候 key 的作用是为了高效的更新渲染虚拟 DOM。
```
---
#### computed，watch，methods的实现原理
```js
1. computed
  - 实际就是vue数据绑定中转站（订阅者部分）watcher实现的
  - computed是通过defineComputed方法来定义computed，此方法会查找vm.$data或props中是否由定义过，所以computed不能和data或props名称相同
  - 是通过订阅者来返回值的，每个computed会实例化一个Dep
  - 相对于其他的watcher，computed不会立即返回值，computed做了缓存
  - 缓存原理
    - 在watch的时候，先判断this.dirty(为true的时候即数据依赖的数据发生变化)
    - 当dirty为true的时候，执行getter，触发computed的计算更新
    - 当为false的时候，不会触发更新，直接返回之前的值

2. watch
  - 利用发布订阅者模式
  - 先遍历所有的wathcer对象，找到需要watch的对象
  - 当数据被观测到变化后
  - 按照传递进来的参数进行对应的处理，如deep
  - 设置了deep后，会递归遍历来达到深度监测

3. methods
  - 遍历methods对象，逐个复制到实例上
  - 利用bind绑定实例来固定作用域
```
---
#### this.$set 的原理
```js
1. 因为js的引用类型的缘故，Vue的监听机制无法监听到引用类型的改变
2. 所以在Vue中，提供了 $set 方法去更新 引用类型的响应式数据 `Vue.protptype.$set = set`
3. 并触发 dep.notity 方法，让订阅者能够接收到改变
```
---
#### 生命周期
```js
Vue 一共有8个生命阶段，分别是创建前、创建后、加载前、加载后、更新前、更新后、销毁前和销毁后，每个阶段对应了一个生命周期的钩子函数。

（1）beforeCreate 钩子函数，在实例初始化之后，在数据监听和事件配置之前触发。因此在这个事件中我们是获取不到 data 数据的。

（2）created 钩子函数，在实例创建完成后触发，此时可以访问 data、methods 等属性。但这个时候组件还没有被挂载到页面中去，所以这个时候访问不到 $el 属性。一般我们可以在这个函数中进行一些页面初始化的工作，比如通过 ajax 请求数据来对页面进行初始化。

（3）beforeMount 钩子函数，在组件被挂载到页面之前触发。在 beforeMount 之前，会找到对应的 template，并编译成 render 函数。

（4）mounted 钩子函数，在组件挂载到页面之后触发。此时可以通过 DOM API 获取到页面中的 DOM 元素。

（5）beforeUpdate 钩子函数，在响应式数据更新时触发，发生在虚拟 DOM 重新渲染和打补丁之前，这个时候我们可以对可能会被移除的元素做一些操作，比如移除事件监听器。

（6）updated 钩子函数，虚拟 DOM 重新渲染和打补丁之后调用。

（7）beforeDestroy 钩子函数，在实例销毁之前调用。一般在这一步我们可以销毁定时器、解绑全局事件等。

（8）destroyed 钩子函数，在实例销毁之后调用，调用后，Vue 实例中的所有东西都会解除绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。

当我们使用 keep-alive 的时候，还有两个钩子函数，分别是 activated 和 deactivated 。用 keep-alive 包裹的组件在切换时不会进行销毁，而是缓存到内存中并执行 deactivated 钩子函数，命中缓存渲染后会执行 actived 钩子函数。
```
---

# VueRouter
#### 1.路由钩子
```js
1）全局的钩子函数 beforeEach 和 afterEach

beforeEach 有三个参数，to 代表要进入的路由对象，from 代表离开的路由对象。next 是一个必须要执行的函数，如果不传参数，那就执行下一个钩子函数，如果传入 false，则终止跳转，如果传入一个路径，则导航到对应的路由，如果传入 error ，则导航终止，error 传入错误的监听函数。

（2）单个路由独享的钩子函数 beforeEnter，它是在路由配置上直接进行定义的。

（3）组件内的导航钩子主要有这三种：beforeRouteEnter、beforeRouteUpdate、beforeRouteLeave。它们是直接在路由组
件内部直接进行定义的。
```
---
#### 2. keep-alive
```js
1. vue的渲染过程
  new Vue -> init -> $mount -> compile -> render -> vnode -> patch -> DOM
2. Vue的渲染是从图中render阶段开始的，
  但keep-alive的渲染是在patch阶段，这是构建组件树（虚拟DOM树），并将VNode转换成真正DOM节点的过程
3. keep-alive 不会生成真正的DOM节点
  Vue在初始化生命周期的时候，为组件实例建立父子关系会根据abstract属性决定是否忽略某个组件。
  在keep-alive中，设置了abstract:true，那Vue就会跳过该组件实例
4. keep-alive包裹的组件是如何使用缓存的
  - 在patch阶段，会执行createComponent函数
  - 在首次加载被包裹组建时，由keep-alive.js中的render函数可知 vnode.componentInstance的值是undfined，keepAlive的值是true，因为keep-alive组件作为父组件，它的render函数会先于被包裹组件执行；那么只执行到i(vnode,false)，后面的逻辑不执行
  - 再次访问被包裹组件时，vnode.componentInstance的值就是已经缓存的组件实例，那么会执行insert(parentElm, vnode.elm, refElm)逻辑，这样就直接把上一次的DOM插入到父元素中
5. 为什么钩子函数后面也没有触发
  - 当vnode.componentInstance和keepAlive同时为true时，不再进入$mount过程，那mounted之前的所有钩子函数（beforeCreate、created、mounted）都不再执行
6. 为什么触发了 activated 钩子 和 deactivated 钩子
  - 在patch的阶段，最后会执行invokeInsertHook函数，而这个函数就是去调用组件实例（VNode）自身的insert钩子
  - 调用了activateChildComponent函数递归地去执行所有子组件的activated钩子函数
  - 相反地，deactivated钩子函数也是一样的原理，在组件实例（VNode）的destroy钩子函数中调用deactivateChildComponent函数
```
---

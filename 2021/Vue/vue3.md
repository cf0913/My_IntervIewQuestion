# Composition Api
1. 特点
  - 解决大组件时使用 option Api 时，代码的可读性随着组件变大而变差
  - 更好的支持 TypeScript
2. 与 mixin 的区别
  - mixin 多了以后 命名会冲突
  - mixin 数据来源不清晰
3. 优点
  - 在逻辑组织和逻辑复用方面，Composition API是优于Options API
  - 因为Composition API几乎是函数，会有更好的类型推断
  - Composition API 对 tree-shaking 友好，代码也更容易压缩
  - Composition API中见不到this的使用，减少了this指向不明的情况
4. 缺点
  - 小型组件没必要使用 composition Api，代码编写会变得复杂
---
# Vue 3.0 做了哪些优化
1. 更小
  - 移除了一些不常用的api
  - 引入tree-shaking，可以将无用模块“剪辑”，仅打包需要的，使打包的整体体积变小了
2. 更快
  - diff算法优化
  - 静态提升
  - 事件监听缓存
  - SSR优化
3. 新增了许多优化
  - 源码使用typescript编写，对TypeScript支持更好
  - 数据劫持改为 Proxy，监听更全面，但是兼容性没那么好
  - 并且让 Proxy 在监听内部深层次的对象变化时，是在 getter 中去递归响应式，不是无脑递归，性能更好
  - 新 composition API 语法，代码复用更好，组件可读性更高
---
# Vue3.0性能提升主要是通过哪几方面体现
1. 编译阶段
  - diff算法优化
    - vue3在diff算法中相比vue2增加了静态标记
    - 其作用是为了会发生变化的地方添加一个flag标记，下次发生变化的时候直接找该地方进行比较
  - 静态提升
    - Vue3中对不参与更新的元素，会做静态提升，只会被创建一次，在渲染时直接复用
    - 这样就免去了重复的创建节点，大型应用会受益于这个改动，免去了重复的创建操作，优化了运行时候的内存占用
  - 事件监听缓存
    - 默认情况下绑定事件行为会被视为动态绑定，所以每次都会去追踪它的变化
    - 开启了缓存后，没有了静态标记。也就是说下次diff算法的时候直接使用
  - SSR优化
    - 当静态内容大到一定量级时候，会用createStaticVNode方法在客户端去生成一个static node，这些静态node，会被直接innerHtml，就不需要创建对象，然后根据对象渲染
2. 源码体积
  - 相比Vue2，Vue3整体体积变小了，除了移出一些不常用的API，再重要的是Tree shanking
  - 任何一个函数，如ref、reavtived、computed等，仅仅在用到的时候才打包
3. 响应式系统
  - vue3采用proxy重写了响应式系统，因为proxy可以对整个对象进行监听，所以不需要深度遍历
    - 可以监听动态属性的添加
    - 可以监听到数组的索引和数组length属性
    - 可以监听删除属性
---
# Vue3.0里为什么要用 Proxy API 替代 defineProperty API
1. Object.defineProperty
  - 检测不到对象属性的添加和删除
  - 数组API方法无法监听到
  - 需要对每个属性进行遍历监听，如果嵌套对象，需要深层监听，造成性能问题
  - Object.defineProperty只能遍历对象属性进行劫持
2. proxy
  - Proxy的监听是针对一个对象的，那么对这个对象的所有操作会进入监听操作
  - Proxy直接可以劫持整个对象，并返回一个新对象，我们可以只操作新的对象达到响应式目的
  - Proxy可以直接监听数组的变化（push、shift、splice）
  - Proxy 不兼容IE，也没有 polyfill, defineProperty 能支持到IE9
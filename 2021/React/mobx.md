# Mobx 思想的实现原理
```js
示例：
const obj = observable({
    a: 1,
    b: 2
})
autoRun(() => {
    console.log(obj.a)
})
obj.b = 3 // 什么都没有发生
obj.a = 2 // observe 函数的回调触发了，控制台输出：2

1. autoRun 的用途
使用 autoRun 实现 mobx-react 非常简单，核心思想是将组件外面包上 autoRun
这样代码中用到的所有属性都会像上面 Demo 一样，
与当前组件绑定，一旦任何值发生了修改，就直接 forceUpdate，而且精确命中，效率最高

2. 依赖收集
autoRun 的专业名词叫做依赖收集，也就是通过自然的使用，来收集依赖
当变量改变时，根据收集的依赖来判断是否需要更新。

3. 实现步骤拆解
为了兼容，Mobx 使用了 Object.defineProperty 拦截 getter 和 setter，
但是无法拦截未定义的变量，为了方便，我们使用 proxy 来讲解，而且可以监听未定义的变量哦

步骤一 存储结构===========================================================
// 存储所有的代理对象
const proxies = new WeakMap()
function isObservable<T extends object>(obj: T) {
  return (proxies.get(obj) === obj)
}
// 存储所有监听，这些监听由 autoRun 注册
const observers = new WeakMap<object, Map<PropertyKey, Set<Observer>>>()
// 存储待观察队列
const queuedObservers = new Set()
// 存储两个全局变量，分别是是否在队列执行中，以及当前执行到的 autoRun
let queued = false
let currentObserver: Observer = null

步骤二 将对象加工可观察=====================================================
// 如果已经存在代理对象了，就直接返回。
function observable<T extends object>(obj: T = {} as T): T {
  return proxies.get(obj) || toObservable(obj)
}
//  toObservable 函数，它做的事情是，实例化代理，并拦截 get set 等方法
// 
const dynamicObject = new Proxy(obj, {
  // ...
  get(target, key, receiver) {
    const result = Reflect.get(target, key, receiver)
    // 如果取的值是对象，优先取代理对象
    const resultIsObject = typeof result === 'object' && result
    const existProxy = resultIsObject && proxies.get(result)
    // 将监听添加到这个 key 上
    if (currentObserver) {
      registerObserver(target, key)
      if (resultIsObject) {
        return existProxy || toObservable(result)
      }
    }
    return existProxy || result
  }),
  set(target, key, value, receiver) {
    // 如果改动了 length 属性，或者新值与旧值不同，触发可观察队列任务
    if (key === 'length' || value !== Reflect.get(target, key, receiver)) {
      queueObservers<T>(target, key)
    }
    // 如果新值是对象，优先取原始对象
    if (typeof value === 'object' && value) {
      value = value.$raw || value
    }
    return Reflect.set(target, key, value, receiver)
  },
  // ...
})
```
---

# Redux vs Mobx
```js
1. Redux 的特点
- 数据流流动很自然，因为任何 dispatch 都会导致广播，需要依据对象引用是否变化来控制更新粒度。
- 如果充分利用时间回溯的特征，可以增强业务的可预测性与错误定位能力。
- 时间回溯代价很高，因为每次都要更新引用，除非增加代码复杂度，或使用 immutable。
- 时间回溯的另一个代价是 action 与 reducer 完全脱节，数据流过程需要自行脑补。原因是可回溯必然不能保证引用关系。
- 引入中间件，其实主要为了解决异步带来的副作用，业务逻辑或多或少参杂着 magic。
- 但是灵活利用中间件，可以通过约定完成许多复杂的工作。
- 对 typescript 支持困难。

2. Mobx的特点
- 数据流流动不自然，只有用到的数据才会引发绑定，局部精确更新，但免去了粒度控制烦恼。
- 没有时间回溯能力，因为数据只有一份引用。
- 自始至终一份引用，不需要 immutable，也没有复制对象的额外开销。
- 没有这样的烦恼，数据流动由函数调用一气呵成，便于调试。
- 业务开发不是脑力活，而是体力活，少一些 magic，多一些效率。
- 由于没有 magic，所以没有中间件机制，没法通过 magic 加快工作效率（这里 magic 是指 action 分发到 reducer 的过程）。
- 完美支持 typescript。

3. 如何选择
- 前端数据流不太复杂的情况，使用 Mobx，因为更加清晰，也便于维护；
- 如果前端数据流极度复杂，建议谨慎使用 Redux，通过中间件减缓巨大业务复杂度，但还是要做到对开发人员尽量透明，如果可以建议使用 typescript 辅助
```
---
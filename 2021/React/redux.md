# Redux
#### redux流程
![redux流程](https://pic1.zhimg.com/80/v2-0b716fd55e986163344ef8df174b99b0_1440w.jpg)

Redux应用的三大原则
1. 单一数据源
2. State是只读的
3. Reducer必须是一个纯函数
---
#### redux原理
1. Redux官方代码库提供了以下几个模块文件：
- applyMiddleware.js
- bindActionCreators.js
- combineReducers.js
- compose.js
- createStore.js

2. compose.js--------------------------------------------------------
```js
export default function compose(...funcs) {
  if (funcs.length ===0) {
    return arg => arg
  }
  if (funcs.length ===1) {
    return funcs[0]
  }
  // 相当于对数组内的所有函数，从右至左，将前一个函数作为后一个函数的入口参数依次返回
  return funcs.reduce((a, b) => (...args) => a(b(...args)))
} 
```

3. bindActionCreators.js----------------------------------------------
```js
// bindActionCreator可以让开发者在不直接接触dispacth的前提下进行更改state的操作
import warning from'./utils/warning'
function bindActionCreator (actionCreator, dispatch) {
  return (...args) => dispatch(actionCreator(...args))
}
export default function bindActionCreators (actionCreators, dispatch) {
  if (typeof actionCreators ==='function') {
    return bindActionCreator(actionCreators, dispatch)
  }
  const keys = Object.keys(actionCreators)
  const boundActionCreators ={}
  for (let i =0; i < keys.length; i++) {
    const key = keys[i]
    const actionCreator = actionCreators[key]
    boundActionCreators[key] = bindActionCreator(actionCreator, dispatch)
  }
  return boundActionCreators
}
```

4. createStore.js----------------------------------------------
- 入口参数：reducer、preloadState、enhancer
- 内部变量：currentReducer、currentState、currentListeners、nextListeners、isDispatching
- 输出：dispatch、subscribe、getState、replaceReducer
![CreateStore的整体架构](https://pic2.zhimg.com/80/v2-717bceafe1990d8195f4484fe8ff60d1_1440w.jpg)

---
#### redux中间件原理
```js
// 调用applyMiddleware
applyMiddleware(thunk, logger)

export default function applyMiddleware(...middlewares) {
  return createStore => (...args) => {
    const store = createStore(...args)    
    let dispatch = () => {throw new Error('Dispatching while')}

    const middlewareAPI = {
      getState: store.getState,
      dispatch: (...args) => {
        return dispatch(...args)
      }
    }

    const chain = middlewares.map(middleware => middleware(middlewareAPI))
    // Redux为了支持中间件，内部重写了原来的dispatch方法，
    // 同时最后传入原来的store.dispatch方法，也就是将原来的disptach方法也当做中间件处理了
    dispatch = compose(...chain)(store.dispatch)

    return {
      ...store,
      dispatch
    }
  }
}
// 传入的中间件被compose函数聚合后改写dispatch方法，
// 所以我们也可以理解成中间件是对dispatch方法的增强,
// 比如说:增强dispatch函数对action是函数、Promise的处理

// 中间件的执行原理和koa中间件的执行原理类似，但是不是洋葱型的，而是半个洋葱，
// 因为redux是单向执行的，走过去就完事了。
// 当你应用了中间件，在触发一个action操作的时候，action操作就会经过先经过中间件，最终再形成dispatch(action)。

// Redux实现中间件的原理核心是增强原来dispatch函数的能力，
// 让函数拥有某种能力自然而然想到高阶函数的处理方式，
// compose 方法将新的 middlewares 和 store.dispatch 结合起来，生成一个新的 dispatch 方法，
// 另外通过改写后的dispatch方法，可以确定Redux中间件也是基于洋葱模型

// 如何写Redux中间件-------------------------
// 例如 redux-thunk
function createThunkMiddleware(extraArgument) {
  return ({ dispatch, getState }) => (next) => (action) => {
    // 如果是函数 thunk来处理
    if (typeof action === 'function') {
      return action(dispatch, getState, extraArgument);
    }
    // 其它处理不了，交给下一个中间件处理
    return next(action);
  };
}
const thunk = createThunkMiddleware();
```
---
#### react-redux原理
```js
react-redux的核心机制是通知订阅模式，源码中有一个Subscription类，它的作用主要是订阅父级的更新和通知子级的更新，
也就是它既可以订阅别人，别人也可以订阅它，同时可以通知订阅它的Subscription

最外层的Provider组件的Context里包含了的store（也就是我们传入的）和生成的Subscription实例，
它的Subscription实例订阅的则是redux 的subscrib()

当我们使用了connect()时，它会生成一个新组件<Component1/>，<Component1/>里会生成一个Subscription实例，
它会订阅父级（这时是Provider）的Subscription实例，
同时将自己的Subscription覆盖进Context，再包装我们传入的组件

在组件挂载完成后，如果store有更新，Provider会通知下一级组件的Subscription，
下一级组件又会通知自己的下一级组件

在订阅的时候，会将更新自己组件的方法通过回调onStateChange()传入父级的Subscription
一旦父级接收到通知，就会循环调用订阅自己的组件的onStateChange来更新它们

更新的原理就是使用我们传入的mapStateToProps和mapDispatchToProps，结合内置的selectorFactor()来对比state和props，一旦有改变就强制更新自己，所以我们传入的WrappedComponent也被强制更新了
```
---
#### redux-thunk的原理
```js
// 例如 redux-thunk
function createThunkMiddleware(extraArgument) {
  return ({ dispatch, getState }) => (next) => (action) => {
    // 如果是函数 thunk来处理
    if (typeof action === 'function') {
      return action(dispatch, getState, extraArgument);
    }
    // 其它处理不了，交给下一个中间件处理
    return next(action);
  };
}
const thunk = createThunkMiddleware();
```
---
#### 简单实现一个redux
[实现一个redux](https://github.com/brickspert/blog/issues/22)
```js
// index.js---------------
import createStore from './createStore';
import combineReducers from './combineReducers';
import applyMiddleware from './applyMiddleware';
export {
  createStore,
  combineReducers,
  applyMiddleware
}

// createStore.js ----------------
export default function createStore(reducer, initState, rewriteCreateStoreFunc) {
  if (rewriteCreateStoreFunc) {
    const newCreateStore = rewriteCreateStoreFunc(createStore);
    return newCreateStore(reducer, initState);
  }
  let state = initState;
  let listeners = [];
  function subscribe(listener) {
    listeners.push(listener);
  }
  function dispatch(action) {
    state = reducer(state, action);
    for (let i = 0; i < listeners.length; i++) {
      const listener = listeners[i];
      listener();
    }
  }
  function getState() {
    return state;
  }
  dispatch({ type: Symbol() });
  return {
    subscribe,
    dispatch,
    getState
  }
}

// combineReducers.js ------------------------------
export default function combineReducers(reducers) {
  const reducerKeys = Object.keys(reducers)
  /*返回合并后的新的reducer函数*/
  return function combination(state = {}, action) {
    /*生成的新的state*/
    const nextState = {}
    /*遍历执行所有的reducers，整合成为一个新的state*/
    for (let i = 0; i < reducerKeys.length; i++) {
      const key = reducerKeys[i]
      const reducer = reducers[key]
      /*之前的 key 的 state*/
      const previousStateForKey = state[key]
      /*执行 分 reducer，获得新的state*/
      const nextStateForKey = reducer(previousStateForKey, action)
      nextState[key] = nextStateForKey
    }
    return nextState;
  }
}

// applyMiddleware.js---------------------------
const applyMiddleware = function (...middlewares) {
  return function (oldCreateStore) {
    /*生成新的 createStore*/
    return function newCreateStore(reducer, initState) {
      /*1. 生成store*/
      const store = oldCreateStore(reducer, initState);
      /*给每个 middleware 传下store，相当于 const logger = loggerMiddleware(store);*/
      /* const chain = [exception, time, logger]*/
      const chain = middlewares.map(middleware => middleware(store));
      let dispatch = store.dispatch;
      /* 实现 exception(time((logger(dispatch))))*/
      chain.reverse().map(middleware => {
        dispatch = middleware(dispatch);
      });
      /*2. 重写 dispatch*/
      store.dispatch = dispatch;
      return store;
    }
  }
}
export default applyMiddleware;
```
---
## redux性能优化
```js
1. 拆分reducer
  - 每个action都触发同一个reducer会拖慢redux的性能，且不易维护
2. 减少store更新事件数量
  - 减少订阅者被调用次数
```
---
## redux的state为什么不能改变
```js
1. redux和react-redux都是使用了浅比较
  - redux的combinaReducers方法是浅比较它调用的reducer的引用是否发生变化
  - react-redux的connect生成的组件都是浅比较根state的变化与mapStateToProps的值来判断是否需要重新渲染
2. 不可变数据提升了数据的安全性
3. 进行时间旅行调试（记录以前的状态）要求reducer是一个没有副作用的纯函数，以此在不同state之间正确的移动
```
---
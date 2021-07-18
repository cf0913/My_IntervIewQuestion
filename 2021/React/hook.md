# hook
## react-Hook
```js
1. 常用的hooks
  - useState
    - `const [count, setCount] = useState(0)`
  - useEffect
    - `useEffect(() => { ... })`
    - 作用相当于componentDidMount生命周期
    - 执行DOM更新后执行Effect的操作
    - 怎么清除：返回一个函数，每次组件卸载的时候都会执行该函数
    - 每次渲染的时候都会执行Effect
    - 优化Effect：`useEffect(() => { ... }, [count])` 第二个参数表明只有该state发生变化时才调用Effect
  - useContext

2. hook结合redux
  - useReducer的hook
  - `const [state, dispatch] = useReducer(reducer, initialArg, init)`
  - 第一个参数是自己定义的reducer，第二个是初始参数，第三个是可选的初始参数的函数
```
---
#### 自定义hook
```js
import { useEffect, useRef } from 'react'

const useDebounce = (fn, ms = 30, deps = []) => {
  let timeout = useRef()
  useEffect(() => {
    if (timeout.current) clearTimeout(timeout.current)
    timeout.current = setTimeout(() => {
      fn()
    }, ms)
  }, deps)
  const cancel = () => {
    clearTimeout(timeout.current)
    timeout = null
  }
  return [cancel]
}

export default useDebounce
```
---
#### hook的原理
```js
// useState的实现 
var _state; // 把 state 存储在外面
function useState(initialValue) {
  _state = _state || initialValue; // 如果没有 _state，说明是第一次执行，把 initialValue 复制给它
  function setState(newState) {
    _state = newState;
    render();
  }
  return [_state, setState];
}
// ---------------------------------------
// useEffect的实现
let _deps; // _deps 记录 useEffect 上一次的 依赖
function useEffect(callback, depArray) {
  const hasNoDeps = !depArray; // 如果 dependencies 不存在
  const hasChangedDeps = _deps
    ? !depArray.every((el, i) => el === _deps[i]) // 两次的 dependencies 是否完全相等
    : true;
  /* 如果 dependencies 不存在，或者 dependencies 有变化*/
  if (hasNoDeps || hasChangedDeps) {
    callback();
    _deps = depArray;
  }
}
// =========================================================================================
// 完整版 setState
let memoizedState = []; // hooks 存放在这个数组
let cursor = 0; // 当前 memoizedState 下标
function useState(initialValue) {
  memoizedState[cursor] = memoizedState[cursor] || initialValue;
  const currentCursor = cursor;
  function setState(newState) {
    memoizedState[currentCursor] = newState;
    render();
  }
  return [memoizedState[cursor++], setState]; // 返回当前 state，并把 cursor 加 1
}
// 完整版 useEffect
function useEffect(callback, depArray) {
  const hasNoDeps = !depArray;
  const deps = memoizedState[cursor];
  const hasChangedDeps = deps
    ? !depArray.every((el, i) => el === deps[i])
    : true;
  if (hasNoDeps || hasChangedDeps) {
    callback();
    memoizedState[cursor] = depArray;
  }
  cursor++;
}

// 真正的react-hook--------------------------------------------
1. React 中是通过类似单链表的形式来代替数组的。通过 next 按顺序串联所有的 hook。
type Hooks = {
	memoizedState: any, // 指向当前渲染节点 Fiber
  baseState: any, // 初始化 initialState， 已经每次 dispatch 之后 newState
  baseUpdate: Update<any> | null, // 当前需要更新的 Update ，每次更新完之后，会赋值上一个 update，方便 react 在渲染错误的边缘，数据回溯
  queue: UpdateQueue<any> | null,// UpdateQueue 通过
  next: Hook | null, // link 到下一个 hooks，通过 next 串联每一 hooks
}
 
type Effect = {
  tag: HookEffectTag, // effectTag 标记当前 hook 作用在 life-cycles 的哪一个阶段
  create: () => mixed, // 初始化 callback
  destroy: (() => mixed) | null, // 卸载 callback
  deps: Array<mixed> | null,
  next: Effect, // 同上 
};

2. memoizedState，cursor 是存在哪里的？如何和每个函数组件一一对应的？
hooks 的数据就作为组件的一个信息，存储在这些节点上，伴随组件一起出生，一起死亡。
```
---
#### 为什么只能在函数最外层调用 Hook？为什么不要在循环、条件判断或者子函数中调用
```js
memoizedState 数组是按 hook定义的顺序来放置数据的，如果 hook 顺序变化，memoizedState 并不会感知到。
```
---
#### 自定义的 Hook 是如何影响使用它的函数组件的？
```js
共享同一个 memoizedState，共享同一个顺序。
```
---
#### “Capture Value” 特性是如何产生的？
```js
每一次 ReRender 的时候，都是重新去执行函数组件了，对于之前已经执行过的函数组件，并不会做任何操作。
```
---

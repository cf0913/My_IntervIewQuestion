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

```
---
#### hook的原理
```js

```
---
# Redux
#### redux流程
```js

```
---
#### redux原理
```js

```
---
#### redux中间件原理
```js

```
---
#### react-redux原理
```js

```
---
#### redux-thunk的原理
```js

```
---
#### 简单实现一个redux
```js

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
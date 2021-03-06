# vue和react的选择
```js
1. vue的特点
- mvvm的设计模式，有很方便的双向绑定
- 语法简便，容易上手
- Pc和H5都适用，配合webpack工程化方便
- 有性能优异的diff算法，节省浏览器重绘开销

2. react的特点
- mvc的设计模式，单向数据流
- 语法复杂，上手难度稍高
- Pc和H5都适用，配合webpack工程化方便
- diff算法优秀，节省浏览器重绘开销

3. vue的优势
- 双向绑定，在一些表单处理方便更加便捷
- 中文环境下的社区资源丰富，容易招人
- vue-router和vuex的全家桶与vue结合很好，使用起来方便
- react是google的，未来可能会有版权纠纷

4. react的优势
- 单向数据流，在表单处理方便，相比双向绑定麻烦，但是在普通场景下性能更好，普通场景下双向绑定显得有点多余
- 除了diff算法外，用户还可以选择 pureComponent/memo 来优化更新逻辑，或者 shouldComponentUpdate/useMemo 自定义更新，vue只有keeplive，所以react的上限更高
- 社区环境好，资源丰富

5. 区别
数据流向的不同。
  - react从诞生开始就推崇单向数据流，
  - 而Vue是双向数据流
数据变化的实现原理不同。
  - react使用的是不可变数据，
  - 而Vue使用的是可变的数据
组件化通信的不同。
  - react中我们通过使用回调函数来进行通信的，
  - 而Vue中子组件向父组件传递消息有两种方式：事件和回调函数
diff算法不同。
  - react主要使用diff队列保存需要更新哪些DOM，得到patch树，再统一操作批量更新DOM。
  - Vue 使用双向指针，边对比，边更新DOM
```

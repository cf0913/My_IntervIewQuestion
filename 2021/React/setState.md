## setState的异步
1. setState的异步
  - 当数据被setState后，该数据就会被标记为脏值，在事件循环结束时，React会一次性重新渲染所有脏值
2. 需要异步的原因
  - 保证内部数据统一
    - 当父组件props传值给子组件的时候，同时setState改变了值，如果是同步的话则两个组件的值不相等
  - 可以并发更新组件（暂未实现，react以后的计划）
    - 让state的改变进行优先级排序，按照优先级来改变
---
## react的渲染原理
1. 编写方式为jsx，或者React.createElement方法，本质都是createElement的方法
  - jsx会被babel转化成React.createElement方法
  - babel编译时会判断，createElement方法的第一个参数会被编译成字符串，当首字母大写时则判断为自定义组件
2. 创建虚拟DOM
  - React.createElement方法来创建虚拟DOM
  - 具体步骤
    - 处理props
    - 获取子元素
    - 处理默认props
  - reactElement
    - 就是react创建的虚拟DOM的element，由各种属性来表示真实DOM的属性
3. 虚拟DOM转化成真实DOM
  - 过程一：初始化参数过程
    - 使用reactDOM.render 方法对组件进行渲染
  - 过程二：批处理，事物调用
  - 过程三：生成html
  - 过程四：渲染html

---
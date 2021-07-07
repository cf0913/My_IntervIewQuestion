# MVVM 和 MVC
#### MVC
1. M: Model
2. V: View
3. C: Control
4. 流程
  - 用户通过 View 层输入数据
  - View 调用 Control 方法改变 Model 的数据
  - Model 的数据改变后更新 View
5. 特点
  - 视图，业务，数据 分离
  - 松耦合
  - 便于代码的维护
6. 缺点
  - view 和 control 联系过于紧密，不利于复用
  - control 过于庞大，业务功能都在 control
![MVC](https://pic3.zhimg.com/80/v2-379cb77a473bf0c7c449f701b8a17ffe_1440w.jpg)

#### MVVM
1. M: Model
2. V: View
3. VM: ViewModel
4. 流程
  - 用户通过 View 触发 ViewModel 的方法
  - ViewModel 通知 Model 修改数据
  - Model 修改完数据后，通知 ViewModel 去更新 View
5. 特点
  - View 和 Model 不能直接通信，需要通过 ViewModel
  - ViewModel 通常要实现一个 Observer 观察者
  - 当数据发生变化，ViewModel 能观察到这种变化，然后通知对应的 View 变化
  - 而当用户操作 View 的时候，ViewModel 也能监听到视图的变化，然后通知 Model 数据改动
![MVVM](https://pic3.zhimg.com/80/v2-10c092ce5f24e44625165228cd4f245e_1440w.jpg)

#### Vue 和 React
1. Vue 是 MVVM 的模式，因为实现了数据的双向绑定
2. React 则是单向数据流，所以是 MVC 的模式
3. MVVM 适合数据驱动的场景，数据操作比较多的场景
4. MVC 则适合中大型项目的分层开发
5. 没有孰优孰劣，只是看是不是自己适合的场景，MVVM 的模式可能会更消耗性能，因为除了要监听数据的变化以外，还要监听视图的变化。但是大多数时候，我们并不需要双向绑定，所以在日常使用中，两者没有多大的差别。当遇到大量的表单的时候，双向绑定使用起来就会更加舒服，但是更加消耗性能，这就看取舍了。
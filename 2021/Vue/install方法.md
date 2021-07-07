# Install方法
#### 1.Install方法原理和vue.use方法原理
```js
1. vue的use方法传入的是一个带install方法的plugin
  - 在将plugin push进数据_installedPlugins前，会先判断plugin是否存在
  - 所以插件只会执行一次
2. install方法的返回值是vue的构造方法
3. 执行plugin函数时，将vue构造方法传给plugin
  - 因此插件内部可以使用vue.mixin方法
  - 对vue生命周期可以进行注入
```
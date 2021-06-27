# Loader
#### 1.loader的特点
```js
1. 可以同步也可以异步
2. loader 运行在 Node.js 中，并且能够执行任何操作
3. 除了常见的通过 package.json 的 main 来将一个 npm 模块导出为 loader，还可以在 module.rules 中使用 loader 字段直接引用一个模块
4. 插件(plugin)可以为 loader 带来更多特性
5. loader 能够产生额外的任意文件
```
---
#### 2.loader的作用
```js
可以通过 loader 的预处理函数，为 JavaScript 生态系统提供更多能力。
用户现在可以更加灵活地引入细粒度逻辑，例如：压缩、打包、语言翻译和更多其他特性
```
---
#### 2.loader的原理
```js
1. 处理单个模块
  1.1 获取模块内容 -> 根据test力设置的规则获取相关文件
  1.2 分析模块内容 -> 不同的loader分析模块方式不同
  1.3 对模块内容做处理 -> 不同的loader处理方式不同
2. 递归的获取所有模块的信息
  - 从入口模块开始，对每个模块以及模块的依赖模块都调用getModuleInfo方法就行分析，最终返回一个包含所有模块信息的对象
3. 生成最终代码
```
---
#### 3.怎么实现一个loader
```js
1. loader本质上就是一个函数，这个函数会在我们在我们加载一些文件时执行
2. 对于loader的编写，一定不要使用箭头函数，那样会改变this的指向, this里面有很多可以用到的东西
3. 官方的loader-utils包可以获取webpack配置的option内容

// 使用loader
resolveLoader: { // resolveLoader配置项，指定loader查找文件路径,这样我们使用loader时候可以直接指定loader的名字
  // loader路径查找顺序从左往右
  modules: ['node_modules', './']
},
module: {
  rules: [
    {
      test: /\.js$/,
      use: 'syncLoader'
    }
  ]
}
// 同步 loader----------
const loaderUtils = require('loader-utils')
module.exports = function (source) {
  const options = loaderUtils.getOptions(this)
  console.log(options)
  source += options.message
  // 可以传递更详细的信息
  this.callback(null, source)
}

// 异步loader-----------
const loaderUtils = require('loader-utils')
module.exports = function (source) {
  const options = loaderUtils.getOptions(this)
  const asyncfunc = this.async()  // 让webpack知道这个loader是异步运行，返回的是和同步使用时一致的this.callback
  setTimeout(() => {
    source += '走上人生颠覆'
    asyncfunc(null, res)
  }, 200)
}
```
---
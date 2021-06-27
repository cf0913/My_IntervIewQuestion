# Plugin
#### Plugin的原理
```js
1. webpack执行时，首先生成了插件的实例对象
2. 之后会调用插件上的apply方法
3. 并将compiler对象(webpack实例对象，包含了webpack的各种配置信息...)作为参数传递给apply
4. 之后我们便可以在apply方法中使用compiler对象去监听webpack在不同时刻触发的各种事件来进行我们想要的操作了。
```
---
#### Plugin与Loader的区别
```js
loader
  - 是一个转换器，将A文件进行编译形成B文件，这里操作的是文件，比如将A.scss转换为A.css，单纯的文件转换过程
  - 执行时机是在 webpack 运行 doBuild时执行

plugin
  - 是一个扩展器，它丰富了webpack本身
  - 在webpack初始化 生成 compiler 对象之后进行初始化，然后根据具体plugin的功能的不同，plugin内部订阅的钩子不同而在不同的时机执行
```
---
#### 如何实现一个plugin
```js
class DemoWebpackPlugin {
  constructor () {
    console.log('plugin init')
  }
  // compiler是webpack实例
  apply (compiler) {
    // 一个新的编译(compilation)创建之后（同步）
    // compilation代表每一次执行打包，独立的编译
    compiler.hooks.compile.tap('DemoWebpackPlugin', compilation => {
      console.log(compilation)
    })
    // 生成资源到 output 目录之前（异步）
    compiler.hooks.emit.tapAsync('DemoWebpackPlugin', (compilation, fn) => {
      console.log(compilation)
      compilation.assets['index.md'] = {
        // 文件内容
        source: function () {
          return 'this is a demo for plugin'
        },
        // 文件尺寸
        size: function () {
          return 25
        }
      }
      fn()
    })
  }
}
module.exports = DemoWebpackPlugin
```
---

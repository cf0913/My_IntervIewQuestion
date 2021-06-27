# webpack基础
#### webpack的基本功能
```js
1. 项目打包压缩
2. 文件转换
3. 本地启动服务
```
---
#### webpack的构建过程
```js
1. webpack-cli入口
  1.1 参数校验处理
  1.2 require webpack
    - webpack启动入口，webpack-cli/bin/cli.js
2. webpack 初始化
  2.1 生成 compiler
    - compiler 可以理解为 webpack 编译的调度中心，是一个编译器实例
    - 在 compiler 对象记录了完整的 webpack 环境信息
    - 在 webpack 的每个进程中，compiler 只会生成一次
    - Compiler 对象继承自 Tapable，初始化时定义了很多钩子
  2.2 初始化默认插件和Option配置
    - 根据配置注册对应的插件
    - 有个比较重要的插件 -> EntryOptionPlugin插件
    - SingleEntryPlugin 插件中订阅了 compiler 的 make 钩子，并在回调中等待执行 addEntry
3. webpack构建阶段
  3.1 run/watch -> 生成 compilation 
    - 开始编译，判断是否是watch，是watch则运行watch，否则运行run
    - 创建此次编译的 Compilation 对象
    - Compilation 对象是后续构建流程中最核心最重要的对象，包含了一次构建过程中所有的数据
    - 一次构建过程对应一个 Compilation 实例
    - 创建 Compilation 实例时会触发钩子 compilaiion 和 thisCompilation
    - 在Compilation对象中
      - modules 记录了所有解析后的模块
      - chunks 记录了所有chunk
      - assets记录了所有要生成的文件
  3.2 make 编译
    - this.hooks.make.callAsync() 执行订阅了 make 钩子的插件的回调函数(上面初始化插件的时候订阅的)
    - 生成modules
      - compilation.addEntry 方法会触发第一批 module 的解析，即我们在 entry 中配置的入口文件 index.js
      - addEntry主要执行_addModuleChain()
        - _addModuleChain中接收参数dependency传入的入口依赖
        - 使用对应的工厂函数NormalModuleFactory.create方法生成一个空的module对象
        - 回调中会把此module存入compilation.modules对象和dependencies.module对象中
        - 由于是入口文件，也会存入compilation.entries中。
        - 随后执行buildModule进入真正的构建module内容的过程。
      - doBuild 方法调用了相应的 loaders 处理文件
      - 调用Parser.parse方法，将JS解析为AST
        - 解析成AST最大作用就是收集模块依赖关系
        - webpack会遍历AST对象，遇到不同类型的节点执行对应的函数
      - 每个 module 解析完成之后，都会触发 Compilation例对象的succeedModule钩子，订阅这个钩子获取到刚解析完的 module 对象
      - 随后，webpack会遍历module.dependencies数组，递归解析它的依赖模块生成module，最终我们会得到项目所依赖的所有 modules
  3.3 seal
    - make 阶段结束后，进入 compilation.seal 方法
    - 生成chunks
      - compilation.seal 方法主要生成chunks，对chunks进行一系列的优化操作，并生成要输出的代码
      - webpack 中的 chunk ，可以理解为配置在 entry 中的模块，或者是动态引入的模块
      - chunk内部的主要属性是_modules，用来记录包含的所有模块对象
      - 所以要生成一个chunk，就先要找到它包含的所有modules
    - 在生成chunk的过程中与过程后，webpack会对chunk和module进行一系列的优化操作，优化操作大都是由不同的插件去完成
    - 可见compilation.seal 方法中，有大量的钩子执行的代码
    - 例如，插件SplitChunksPlugin订阅了compilation的optimizeChunksAdvanced钩子
  3.4 emit
    - 需要生成最终的代码，主要在compilation.seal 中调用了 compilation.createChunkAssets方法
    - createChunkAssets方法会遍历chunks，来渲染每一个chunk生成代码
    - 每个chunk的源码生成之后，会调用 emitAsset 将其存在 compilation.assets 中
    - 当所有的 chunk 都渲染完成之后，assets 就是最终更要生成的文件列表
    - 在 Compiler 开始生成文件前，钩子 emit 会被执行
  3.5 done
    - webpack 会直接遍历 compilation.assets 生成所有文件，然后触发钩子done，结束构建流程
```
---
#### Chunk
```js
Chunk是Webpack打包过程中，一堆module的集合。
我们知道Webpack的打包是从一个入口文件开始，也可以说是入口模块，入口模块引用这其他模块，模块再引用模块。
Webpack通过引用关系逐个打包模块，这些module就形成了一个Chunk。

产生chunk的途径
  - entry入口
    - entry 配置成对象的形式，就会生成多个chunk
    - 此时chunk的name就是对应的key
  - 异步加载模块
  - 代码分割（code spliting）
```
---
#### webpack的优缺点
```js
优点：
  1. 模块化打包
  2. webpack-plugin丰富
  3. 按需加载

缺点：
  1. 传统技术开发的复杂项目不适用
  2. 侵入性较强
  3. 配置多且杂
```
---
#### 如何配置多入口项目
```js

```
---
#### css是如何打包的
```js

```
---
#### 对 webpack 的看法
```
我当时使用 webpack 的一个最主要原因是为了简化页面依赖的管理，并且通过将其打包为一个文件来降低页面加载时请求的资源
数。

我认为 webpack 的主要原理是，它将所有的资源都看成是一个模块，并且把页面逻辑当作一个整体，通过一个给定的入口文件，webpack 从这个文件开始，找到所有的依赖文件，将各个依赖文件模块通过 loader 和 plugins 处理后，然后打包在一起，最后输出一个浏览器可识别的 JS 文件。

Webpack 具有四个核心的概念，分别是 Entry（入口）、Output（输出）、loader 和 Plugins（插件）。

Entry 是 webpack 的入口起点，它指示 webpack 应该从哪个模块开始着手，来作为其构建内部依赖图的开始。

Output 属性告诉 webpack 在哪里输出它所创建的打包文件，也可指定打包文件的名称，默认位置为 ./dist。

loader 可以理解为 webpack 的编译器，它使得 webpack 可以处理一些非 JavaScript 文件。在对 loader 进行配置的时候，test 属性，标志有哪些后缀的文件应该被处理，是一个正则表达式。use 属性，指定 test 类型的文件应该使用哪个 loader 进行预处理。常用的 loader 有 css-loader、style-loader 等。

插件可以用于执行范围更广的任务，包括打包、优化、压缩、搭建服务器等等，要使用一个插件，一般是先使用 npm 包管理器进行安装，然后在配置文件中引入，最后将其实例化后传递给 plugins 数组属性。

使用 webpack 的确能够提供我们对于项目的管理，但是它的缺点就是调试和配置起来太麻烦了。但现在 webpack4.0 的免配置一定程度上解决了这个问题。但是我感觉就是对我来说，就是一个黑盒，很多时候出现了问题，没有办法很好的定位。
```
---
#### webpack的热更新原理
```
1. 通过webpack-dev-server创建两个服务器：提供静态资源的服务（express）和Socket服务
2. express server 负责直接提供静态资源的服务（打包后的资源直接被浏览器请求和解析）
3. socket server 是一个 websocket 的长连接，双方可以通信
4. 当 socket server 监听到对应的模块发生变化时，会生成两个文件.json（manifest文件）和.js文件（update chunk）
5. 通过长连接，socket server 可以直接将这两个文件主动发送给客户端（浏览器）
6. 浏览器拿到两个新的文件后，通过HMR runtime机制，加载这两个文件，并且针对修改的模块进行更新
```
---
#### 怎么保持自己写的插件中的react和项目使用的react版本一致
```js
在package.json中使用 peerdependencies
```
---
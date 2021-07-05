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


// 简易版本
1. 初始化参数：从配置文件和 Shell 语句中读取与合并参数,得出最终的参数。
2. 开始编译：用上一步得到的参数初始化 Compiler 对象,加载所有配置的插件,执行对象的 run 方法开始执行编译。
3. 确定入口：根据配置中的 entry 找出所有的入口文件。
4. 编译模块：从入口文件出发,调用所有配置的 Loader 对模块进行翻译,再找出该模块依赖的模块,再递归本步骤直到所有入口依赖的文件都经过了本步骤的处理。
5. 完成模块编译：在经过第 4 步使用 Loader 翻译完所有模块后,得到了每个模块被翻译后的最终内容以及它们之间的依赖关系。
6. 输出资源：根据入口和模块之间的依赖关系,组装成一个个包含多个模块的 Chunk,再把每个 Chunk 转换成一个单独的文件加入到输出列表,这步是可以修改输出内容的最后机会。
7. 输出完成：在确定好输出内容后,根据配置确定输出的路径和文件名,把文件内容写入到文件系统。

在以上过程中,Webpack 会在特定的时间点广播出特定的事件,插件在监听到感兴趣的事件后会执行特定的逻辑,并且插件可以调用 Webpack 提供的 API 改变 Webpack 的运行结果
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
entry 设置为对象/数组

设置成对象和数组的区别：
配置成对象的形式就会生成多个chunk，chunk的名字就是对象的key
```
---
#### css是如何打包的
```js
使用style-loader,css-loader,postCss-loader

可以打包进js，也可以单独打包成静态资源

style-loader 的作用
将css的内容挂载到页面中

css-loader 的作用
将css模块加载到js中

scss-loader 的作用
编译scss

几个loader的顺序
loader是从右往左解析的
```
---
#### 对 webpack 的看法
```
webpack 就像一条生产线,要经过一系列处理流程后才能将源文件转换成输出结果。 这条生产线上的每个处理流程的职责都是单一的,多个流程之间有存在依赖关系,只有完成当前处理后才能交给下一个流程去处理。 插件就像是一个插入到生产线中的一个功能,在特定的时机对生产线上的资源做处理。

webpack 通过 Tapable 来组织这条复杂的生产线。 webpack 在运行过程中会广播事件,插件只需要监听它所关心的事件,就能加入到这条生产线中,去改变生产线的运作。 webpack 的事件流机制保证了插件的有序性,使得整个系统扩展性很好

Entry
入口起点(entry point)指示 webpack 应该使用哪个模块,来作为构建其内部依赖图的开始。
进入入口起点后,webpack 会找出有哪些模块和库是入口起点（直接和间接）依赖的。
每个依赖项随即被处理,最后输出到称之为 bundles 的文件中

Output
output 属性告诉 webpack 在哪里输出它所创建的 bundles,以及如何命名这些文件,默认值为 ./dist
基本上,整个应用程序结构,都会被编译到你指定的输出路径的文件夹中

Module
模块,在 Webpack 里一切皆模块,一个模块对应着一个文件。Webpack 会从配置的 Entry 开始递归找出所有依赖的模块

Chunk
代码块,一个 Chunk 由多个模块组合而成,用于代码合并与分割

Loader
loader 让 webpack 能够去处理那些非 JavaScript 文件（webpack 自身只理解 JavaScript）。
loader 可以将所有类型的文件转换为 webpack 能够处理的有效模块,然后你就可以利用 webpack 的打包能力,对它们进行处理。
本质上,webpack loader 将所有类型的文件,转换为应用程序的依赖图（和最终的 bundle）可以直接引用的模块

Plugin
loader 被用于转换某些类型的模块,而插件则可以用于执行范围更广的任务。
插件的范围包括,从打包优化和压缩,一直到重新定义环境中的变量。插件接口功能极其强大,可以用来处理各种各样的任务
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
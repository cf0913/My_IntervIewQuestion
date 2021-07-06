# devServer
#### 1.dev-server原理
```js
1. 从命令行和配置文件收集webpack的配置信息
2. 调用 processOptions 对收集的配置信息进行处理后，生成一个option传递给 webpack的config 和 webpack-dev-server
3. 调用 startDevServer 方法
  - 先调用 webpack 函数实例化了 compiler（没传回调函数，则webpack不会直接运行，而是返回 webpack compiler 的实例）
  - 拿到 webpack compiler 实例和先前的 webpack-dev-server 的 options 就去实例化 Server
4. 调用 Server 类的 listen 方法，就正式开启监听请求
  - 注入客户端代码，绑定 webpack compiler 钩子（主要是 done 钩子）
  - 实例化 express 服务器，添加 webpack-dev-middleware 中间件用于处理静态资源的请求
  - 初始化 HTTP 服务器
  - 监听回调里再初始化 websocket 的服务端
```
---
#### 2.webpack怎么做到热更新
```js
如果设置了 hot: true 客户端就会引入 webpack/hot/emitter，触发一个 webpackHotUpdate 事件，将 hash 值传递过去。
所以每次 websocket 更新的都是hash，然后替换页面上文件的hash值，达到热更新。

webpackHotUpdate事件是在 webpack/hot/dev-server.js 文件中处理的，在这里会去检查是否可以更新
如果更新失败就会刷新整个页面来降级实现代码更新的功能

模块更新依赖判断：
1. module.hot.check 方法位于 webpack/lib/HotModuleReplacement.runtime.js 中，是 webpack 内置的HotModuleReplacementPlugin 注入在 webpack bootstrap runtime 中的
2. 在 webpack 使用了 HotModuleReplacementPlugin 编译时，每次增量编译就会多产出两个 hot-update.json 文件
3. 分别表示 chunk 更新的 manifest文件和更新过后的 chunk 文件
4. 那么浏览器端调用 hotDownloadManifest 方法去下载模块更新的 manifest.json 文件，然后调用 hotDownloadUpdateChunk 方法使用 jsonp 的方式下载需要更新的 chunk
5. hotDownloadUpdateChunk 下载完成后调用 webpackHotUpdate 回调。
6. 回调内拿到更新的模块，然后从模块自身开始进行冒泡，如果发现只要有一条祖先路径没有 accept 这次改动就直接刷新页面实行降级强制更新, 如果有被 accept, 就会替换掉原来 webpack runtime 里 module 里旧的模块，然后再执行 accept 的 callback 进行更新
7. hotCheck 方法就是和服务器进行通信拿到更新过后的 chunk，下载好 chunk 后就开始执行 HMR runtime 里的 webpackHotUpdate 回调
8. 经过一系列方法调用然后来到 hotApplyInternal 方法，这个方法把更新过后的模块 apply 到业务中
9. 把更新过的模块进行遍历，找到被该模块影响到的祖先模块，返回一个结果，如果结果标识为 unaccepted 就会被抛出错误，然后走到 webpack/hot/dev-server.js 里的 catch 进行页面级刷新。如果被 accept 的话就会执行后面的 apply 的逻辑。
10. runtime 里声明了 installedModules 这个变量，里面缓存了所有被 __webpack_require__ 调用后加载过的模块，还有 modules 这个变量存储了所有模块。
11. 如果模块有被 accept 的话，那么就会从 installedModules 里删掉旧的模块，把模块从父子依赖中删除，然后把 modules 里面的模块替换成新的模块
12. 最后新模块的代码是在 accept 方法执行后，在触发其回调执行之前被执行
```
---
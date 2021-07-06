# webpack的优化
1. 区分 develop 和 production 环境，不同环境不同配置
2. production 环境关闭 sourceMap
3. 使用 happypack，让loader 可以使用多进程处理文件
4. 配置 external，减少不必要的打包，例如 react
5. 拆分项目，分开打包，或者打包多入口
6. 配置 DLLPlugin
  - 通过单独的webpack配置文件指定哪些公用基础库生成dll，和 external 类似
  - dllPlugin相对external的好处是更加动态化，如果后续哪个包升级了，可以自动处理，external 则需要手动处理
7. 配置 babel-loader 的 cacheDirectory: true
  - 缓存 babel-loader 的执行结果
  - 之后的 webpack 构建，将会尝试读取缓存，来避免在每次执行时，可能产生的、高性能消耗的 Babel 重新编译过程
8. 配置 optimization.splitChunks, 以一定规则划分多个bundle文件，将每个文件体积减小
9. 压缩 css，html 等静态资源
10. 开启 gzip 压缩
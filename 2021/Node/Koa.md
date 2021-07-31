# Koa
#### koa原理
```js
首先 三个静态类分别是Request，Context，Response
  - Request中包含了一些操作 Node原生请求对象的非常有用的方法，例如获取query数据，获取请求url等
  - Response中包含了一些用于设置状态码啦，主体数据啦，header啦，等一些用于操作响应请求的方法
  - Context是koa中最重要的概念之一，Context字面意思是上下文，也有环境等意思，koa中的操作都是基于这个context进行的
    - Context中有两部分，
    - 一部分是自身属性，主要是应用于框架内部使用
    - 一部分是Request和Response委托的操作方法，主要为提供给用户更方便从Request获取想要的参数和更方便的设置Response内容
启动服务
  - 第一步是init一个app对象
    module.exports = Application;
    function Application() {
      if (!(this instanceof Application)) return new Application;
      this.env = process.env.NODE_ENV || 'development';
      this.subdomainOffset = 2;
      this.middleware = []; // 初始化 中间件数组
      this.proxy = false;
      this.context = Object.create(context);  // 绑定 context
      this.request = Object.create(request);  // 绑定 request
      this.response = Object.create(response);  // 绑定 response
    }
  - 第二步是用app对象监听下端口号
    app.listen = function(){
      var server = http.createServer(this.callback());
      return server.listen.apply(server, arguments);
    }
中间件
  - 洋葱圈模型，先进后出
  - 源码里 compose 函数对中间件数组进行处理，当客户端请求时，先 await 各个中间件，然后 next()，依次向上next直到最开始的一个
```
---
#### koa中间件
```js
洋葱圈模型，先进后出
源码里 compose 函数对中间件数组进行处理，当客户端请求时，先 await 各个中间件，然后 next()，依次向上next直到最开始的一个
```
---
#### app.use原理
```js
app.use 加载用于处理http请求的middleware（中间件），当一个请求来的时候，会依次被这些 middlewares处理
```
---
#### koa和express区别
```js
express 使用的回调函数解决异步问题
express 中间件是链式调用

koa 使用 Generator 语法解决异步问题
koa 中间件使用洋葱圈模型
koa 有ctx对象，封装了request和response的操作方法，便于编写
```
---

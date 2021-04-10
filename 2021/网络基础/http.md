# Http
#### 1.http1.0和http2.0的区别
```js

```
---
#### 2.https的优缺点
```js

```
---
#### 3.常见http状态码
```js

```
---
#### 4.常见请求方式与restful
```js

```
---
#### 5.SSL协议
```js

```
---
#### 6.https加密原理
```js

```
---
#### 7.管线化
```js

```
---
#### 8.协议的分层
```js

```
---
#### 9.协议的分层
```js

```
---
#### 10.浏览器第一次请求资源的过程
```js

```
---
#### 11. URL 和 URI 的区别
```js
URI: Uniform Resource Identifier      指的是统一资源标识符
URL: Uniform Resource Location        指的是统一资源定位符
URN: Universal Resource Name          指的是统一资源名称

URI 指的是统一资源标识符，用唯一的标识来确定一个资源，它是一种抽象的定义，也就是说，不管使用什么方法来定义，只要能唯一的标识一个资源，就可以称为 URI。

URL 指的是统一资源定位符，URN 指的是统一资源名称。URL 和 URN 是 URI 的子集，URL 可以理解为使用地址来标识资源，URN 可以理解为使用名称来标识资源。
```
---
#### 12. 常用的几种 Content-Type
```js
（1）application/x-www-form-urlencoded

浏览器的原生 form 表单，如果不设置 enctype 属性，那么最终就会以 application/x-www-form-urlencoded 方式提交数据。该种方式提交的数据放在 body 里面，数据按照 key1=val1&key2=val2 的方式进行编码，key 和 val 都进行了 URL
转码。

（2）multipart/form-data

该种方式也是一个常见的 POST 提交方式，通常表单上传文件时使用该种方式。

（3）application/json

告诉服务器消息主体是序列化后的 JSON 字符串。

（4）text/xml

该种方式主要用来提交 XML 格式的数据。
```
---
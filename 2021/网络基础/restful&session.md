# Restful
```
REST，表示性状态转移（representation state transfer）。
简单来说，就是用URI表示资源，用HTTP方法(GET, POST, PUT, DELETE)表征对这些资源的操作

设计原则和规范
  - 资源。首先要明确资源就是网络上的一个实体，可以是文本、图片、音频、视频。
  - 统一接口。对于业务数据的CRUD，RESTful 用HTTP方法与之对应。
  - URI。统一资源标识符，它可以唯一标识一个资源。
  - 无状态。 所谓无状态是指所有资源都可以用URI定位，而且这个定位与其他资源无关，不会因为其他资源的变动而变化
  - URL中只能有名词，不能出现动词
```
---
# 服务端Session 和 客户端Session
```
客户端session：即浏览器cookie
  - 每次请求时自动带上cookie
  - 因明文存储，故不适合做鉴权
  - 大小限制每条4k

服务端session：即服务上的一块内存区域
  - Session是另一种记录客户状态的机制，不同的是Cookie保存在客户端浏览器中，而Session保存在服务器上
  - 客户端浏览器访问服务器的时候，服务器把客户端信息以某种形式记录在服务器上
  - 客户端浏览器再次访问时只需要从该Session中查找该客户的状态就可以了
  - 步骤
    - 浏览器首次访问，生成JESSIONID保存在服务端session中，且将JESSIONID存储于cookie
    - 后续每次请求直接验证客户端带来的JESSIONID即可，根据JESSIONID从session中获取用户相关信息
    - 真正的数据存储在服务端，客户端只有一个字段，安全性更高
  - Session保存在服务器端。为了获得更高的存取速度，服务器一般把Session放在内存里
  - Session生成后，只要用户继续访问，服务器就会更新Session的最后访问时间，并维护该Session
  - 为防止内存溢出，服务器会把长时间内没有活跃的Session从内存删除。这个时间就是Session的超时时间。如果超过了超时时间没访问过服务器，Session就自动失效了

redis存储session
  - redis是一个高性能的key-value数据库
  - 特点
    - 支持数据持久化，可以将内存中的数据保存在磁盘中，重启的适合可以再次加载进行使用
    - 不仅支持简单的key-value类型的数据，同时还提供list、set、zset、hash等数据结构的存储
    - 支持数据的备份，即master-slave模式的数据备份
  - 优势
    - 性能高
    - 丰富的数据类型
    - 所有操作都是原子性的，即要么成功执行要么失败完全不执行

Session的同步
  - 服务器之间相互拷贝
  - 使用独立缓存服务器存储
```
---

1. js的数据类型，及判断方法
```js
// 数据类型
number string array object blooen null undefined

// 判断方法
typeof      // typeof(a)
instanceof  // a instanceof Array
constructor     // c.constructor === Array
prototype   // Object.prototype.toString.call(a) === ‘[object String]’
jquery.type // jQuery.type( undefined ) === "undefined"
isArray
```

2. 闭包
```js
// 先讲js的作用域，函数外部不可访问内部，内部可访问外部，形成作用域链
// 闭包是一个具有封闭的对外不公开的，包裹结构或空间
// 再讲想要私有化作用域，就可用闭包来实现
// 好处：变量私有化，不影响外部
// 坏处：可能造成内存泄漏
// 用途：沙箱模式，插件，库的编写，组件化的实现
for(var i=0; i<5; i++){
    console.log(i);
}

for(var i=0; i<5; i++){
    (function(i){
        console.log(i);
    })(i)
}
```

3. 前端跨域
```js
// 1. 跨域的原理：端口，协议，域名不同造成跨域
// 2. 跨域上浏览器的安全策略，实际上数据已经从后台返回，是浏览器阻止了
// 3. jsonp的方式：是get的方式，通过script标签的src属性，src的链接携带信息来获取的数据，不安全
// 4. window.domain + iframe
// 5. window.name + iframe
// 6. webpack的proxy配置代理
// 7. nginx配置代理
// 8. 将浏览器的安全策略关闭，chrome有插件
// 9. cors
```

4. 继承
```js
// 原型链继承 =》 简单，但是修改原型后，被继承的原型页修改了
function Super(){
    this.val = 1;
    this.arr = [1];
}
function Sub(){
    // ...
}
Sub.prototype = new Super(); 

// 借用构造函数继承   =》 缺点：无法实现函数复用
// 借父类的构造函数来增强子类实例，等于是把父类的实例属性复制了一份给子类实例装上了（完全没有用到原型）
function Super(val){
    this.val = val;
    this.arr = [1];
 
    this.fun = function(){
        // ...
    }
}
function Sub(val){
    Super.call(this, val);   // 核心
    // ...
}
new Sub();

// 组合继承（最常用）=》缺点：原型上的对象重复了
// 把实例函数都放在原型对象上，以实现函数复用。同时还要保留借用构造函数方式的优点，通过Super.call(this);继承父类的基本属性和引用属性并保留能传参的优点；通过Sub.prototype = new Super();继承父类函数，实现函数复用
function Super(){
    // 只在此处声明基本属性和引用属性
    this.val = 1;
    this.arr = [1];
}
    //  在此处声明函数
Super.prototype.fun1 = function(){};
Super.prototype.fun2 = function(){};
    //Super.prototype.fun3...
function Sub(){
    Super.call(this);   // 核心
    // ...
}
Sub.prototype = new Super();    // 核心


// 寄生组合继承（最佳方式）
function beget(obj){   // 生孩子函数 beget：龙beget龙，凤beget凤。
    var F = function(){};
    F.prototype = obj;
    return new F();
}
function Super(){
    // 只在此处声明基本属性和引用属性
    this.val = 1;
    this.arr = [1];
}
    //  在此处声明函数
Super.prototype.fun1 = function(){};
Super.prototype.fun2 = function(){};
    //Super.prototype.fun3...
function Sub(){
    Super.call(this);   // 核心
    // ...
}
var proto = beget(Super.prototype); // 核心
proto.constructor = Sub;            // 核心
Sub.prototype = proto;              // 核心

// 原型式
function beget(obj){   // 生孩子函数 beget：龙beget龙，凤beget凤。
    var F = function(){};
    F.prototype = obj;
    return new F();
}
function Super(){
    this.val = 1;
    this.arr = [1];
}
 
    // 拿到父类对象
var sup = new Super();
    // 生孩子
var sub = beget(sup);   // 核心

// 寄生式
function beget(obj){   // 生孩子函数 beget：龙beget龙，凤beget凤。
    var F = function(){};
    F.prototype = obj;
    return new F();
}
function Super(){
    this.val = 1;
    this.arr = [1];
}
function getSubObject(obj){
    // 创建新对象
    var clone = beget(obj); // 核心
    // 增强
    clone.attr1 = 1;
    clone.attr2 = 2;
    //clone.attr3...
 
    return clone;
}
var sub = getSubObject(new Super());




// ES6的class继承
function Sub(){
    this.color = 'red'
}
class NewSub extends Sub{
    constructor(props){
        super(props)
        this.color = props.color
    }
}
var subs = new NewSub()
console.log(subs.color)
```

5. js的节流和防抖
```js
// 函数节流和函数防抖，两者都是优化高频率执行js代码的一种手段
// 函数节流的要点是，声明一个变量当标志位，记录当前代码是否在执行。如果空闲，则可以正常触发方法执行。 如果代码正在执行，则取消这次方法执行
// 例如监听页面滚动
var canRun = true;
document.getElementById("throttle").onscroll = function(){
    if(!canRun){
        // 判断是否已空闲，如果在执行中，则直接return
        return;
    }

    canRun = false;
    setTimeout(function(){
        console.log("函数节流");
        canRun = true;
    }, 300);
};

// 函数防抖的应用场景，最常见的就是用户注册时候的手机号码验证和邮箱验证了。只有等用户输入完毕后，前端才需要检查格式是否正确，如果不正确，再弹出提示语
// 函数防抖的要点，也是需要一个setTimeout来辅助实现。延迟执行需要跑的代码。如果方法多次触发，则把上次记录的延迟执行代码用clearTimeout清掉，重新开始。如果计时完毕，没有方法进来访问触发，则执行代码。
// 函数防抖
var timer = false;
document.getElementById("debounce").onscroll = function(){
    clearTimeout(timer); // 清除未执行的代码，重置回初始化状态

    timer = setTimeout(function(){
        console.log("函数防抖");
    }, 300);
};  

```

6. JS中的事件绑定，事件捕获，事件冒泡以及事件委托
```js
// 事件分为三个阶段：   事件捕获 -->  事件目标 -->  事件冒泡
// 事件捕获：事件发生时（onclick,onmouseover……）首先发生在document上，然后依次传递给body、&hellip;&hellip;最后到达目的节点（即事件目标）
// 事件冒泡：子元素事件会触发父元素事件,一层层向上
// 阻止冒泡：e.stopPropagation()或者cancel.bubble
// 阻止默认事件：return false 或 event.preventDefault(); 

// 事件委托：利用事件冒泡的特性，将里层的事件委托给外层事件，根据event对象的属性进行事件委托，改善性能。
// 可用于未创建的元素，将事件绑定到其父元素，待创建完毕后，事件也会触发
```

7. ajax的请求方式
```js
// 优点：局部刷新， 异步与服务器通信， 前后端负载平衡
// 缺点：不利于seo， 浏览器无法后退， 安全问题
// 基本步骤
    var xhr = new XMLHttpRequest();
    xhr.timeout = 3000;
    xhr.ontimeout = function (event) {
        alert("请求超时！");
    }
    var formData = new FormData();
    formData.append('tel', '18217767969');
    formData.append('psw', '111111');
    xhr.open('POST', 'http://www.test.com:8000/login');
    xhr.send(formData);
    xhr.onreadystatechange = function () {
        if (xhr.readyState == 4 && xhr.status == 200) {
            alert(xhr.responseText);
        }
        else {
            alert(xhr.statusText);
        }
    }
```

8. this的指向问题
```js
// this是一个关键字
// 当new对象或者方法的时候，this就会引用该对象，同时继承该对象
// 函数有四种调用模式，当为方法调用的时候，this指调用的方法；当位函数调用模式的时候，this指向window；当位构造器调用的时候，this指实例出来的对象；当为apply调用的时候，this会被指定。
```

9. 对面向对象的理解
```js
// 封装，继承，多态 =》就是oop的主流思想
// 通俗来说就是面向过程的封装，达到可复用
// 构造函数就是一种封装，闭包是一种封装，可以复用
// 对象继承分两种情况，一种是构造函数的继承，一种是原型（prototype）的继承
// 多态：一个对象，多种形态，js中不支持多态
```

10. 对es6的掌握
```js
// let const
// 对象的书写方式
// 箭头函数
// 解构赋值
// 剩余参数
// promise then
// class
// async awith
// module export
```

11. call和apply
```js
// call和apply的第一个参数都是要传入当前对象，即函数内部的this
// 后面的参数都是传递给当前对象的参数
// call除了this的指向，后面的参数是单个的参数
// apply后面的上参数数组
// apply可以用来展开数组
function.apply(null, arr); // 相当于函数传入了多个参数
// ES6中可用剩余参数。。。arg代替
```

12. 原型链
```js
// js是面向对象的，每个实例都有一个非标准属性__proto__，它指向原型对象
// 这个实例对象的构造函数有一个原型属性prototype，与实例的__proto__指向同一个对象
// 每个对象都有自己的constructor，它指向该对象的构造函数
// 当一个对象在查找一个属性的时候，自身没有，就根据__proto__向它的原型上查找，如果都没有，则向它的原型的原型查找，直到找到object.prototype.__proto__为null的时候停止
// 这样也就形成了原型链
```

13. websocket
```js
// 使用ws或wss协议，用于任意客户端和服务端程序，可长连接，用于推送数据
// API

// 创建websocket
var ws = new WebSocket("wss://echo.websocket.org");

ws.onopen = function(evt) { 
  console.log("Connection open ..."); 
  ws.send("Hello WebSockets!");
};

ws.onmessage = function(evt) {
  console.log( "Received Message: " + evt.data);
  ws.close();
};

ws.onclose = function(evt) {
  console.log("Connection closed.");
};     
```

14. new字符干了什么
```js
// 创建了一个空对象，并且this变量引用了该对象，同时继承了该对象的原型
// 属性和方法被加入到this引用的对象中
```

15. 函数的调用方式
```js
// 方法调用模式 
    var myobject={
            value:0,
            inc:function(){
                alert(this.value)；  // this指的是myobject
            }
        }
    myobject.inc()
// 函数调用模式
    var add=function(a,b){
        alert(this)     // this被绑顶到window
            return a+b;
        }
    var sum=add(3,4);
    alert(sum)
// 构造器调用模式
    var quo=function(string){
            this.status=string;
        }
    quo.prototype.get_status=function(){
            return this.status;     // this指qq
        }
    var qq=new quo("aaa");
    alert(qq.get_status());
// apply调用模式
    var arr=[10,20];
    var sum=add.apply(myobject,arr);
    alert(sum);
```

16. ES6的新特性
- let const 块级作用域
- 箭头函数，剩余参数，在对象中的简洁表示
- 对象的扩展：解构赋值，Object.values, Object.keys, Object.entries, Object.assign
- promise
- async await
- class
- module export


17. 用ES6实现继承
```js
function Sub() {
    this.color = 'red'
}
Sub.prototype.add = function(a,b){
    return a + b
}

class Temp extends Sub{
    constructor(props){
        super(props)
    }
}

const a = new Temp()
console.log(a.color)
console.log(a.add(1,2))
```

18. 对象的深拷贝
```js
const obj = {a:1,b:{x:1,y:2}}
// JSON.Stringify()

// 递归

// $.extend({}, obj)

// Object.assign()
```

19. 用ES5实现promise
```js
function MyPromise(fn) {
  this.value;
  this.status = 'pending';
  this.resolveFunc = function() {};
  this.rejectFunc = function() {};
  fn(this.resolve.bind(this), this.reject.bind(this));
}

MyPromise.prototype.resolve = function(val) {
  var self = this;
  if (this.status == 'pending') {
    this.status = 'resolved';
    this.value = val;
    setTimeout(function() {
      self.resolveFunc(self.value);
    }, 0);
  }
}

MyPromise.prototype.reject = function(val) {
  var self = this;
  if (this.status == 'pending') {
    this.status = 'rejected';
    this.value = val;
    setTimeout(function() {
      self.rejectFunc(self.value);
    }, 0);
  }
}

MyPromise.prototype.then = function(resolveFunc, rejectFunc) {
  var self = this;
  return new MyPromise(function(resolve_next, reject_next) {
    function resolveFuncWrap() {
      var result = resolveFunc(self.value);
      if (result && typeof result.then === 'function') {
        //如果result是MyPromise对象，则通过then将resolve_next和reject_next传给它
        result.then(resolve_next, reject_next);
      } else {
        //如果result是其他对象，则作为参数传给resolve_next
        resolve_next(result);
      }
    }
    function rejectFuncWrap() {
      var result = rejectFunc(self.value);
      if (result && typeof result.then === 'function') {
        //如果result是MyPromise对象，则通过then将resolve_next和reject_next传给它
        result.then(resolve_next, reject_next);
      } else {
        //如果result是其他对象，则作为参数传给resolve_next
        resolve_next(result);
      }
    }
    self.resolveFunc = resolveFuncWrap;
    self.rejectFunc = rejectFuncWrap;
  })
}
```

20. 用promise实现fetch
```js
import React, {Component} from 'react';

/**
 * 使用Promise封装Fetch，具有网络超时、请求终止的功能
 */
class NetUtil extends Component {

    static baseUrl = "http://xxxx:81/api/";
    static token = '';

    /**
     * post请求
     * url : 请求地址
     * data : 参数(Json对象)
     * callback : 回调函数
     * */
    static fetch_request(url, method, params = '') {
        let header = {
            'Accept': 'application/json',
            'Content-Type': 'application/json;charset=UTF-8',
            'accesstoken': NetUtil.token,
        }
        let promise = null;
        if (params == '') {
            promise = new Promise((resolve, reject) => {
                fetch(NetUtil.baseUrl + url, {method: method, headers: header})
                    .then(response => response.json())
                    .then(responseData => resolve(responseData))
                    .then(err => reject(err))
            })
        } else {
            promise = new Promise((resolve, reject) => {
                fetch(NetUtil.baseUrl + url, {method: method, headers: header, body: JSON.stringify(params)})
                    .then(response => response.json())
                    .then(responseData => resolve(responseData))
                    .then(err => reject(err))
            })
        }
        return NetUtil.warp_fetch(promise);
    }

    /**
     * 创建两个promise对象，一个负责网络请求，另一个负责计时，如果超过指定时间，就会先回调计时的promise，代表网络超时。
     * @param {Promise} fetch_promise    fetch请求返回的Promise
     * @param {number} [timeout=10000]   单位：毫秒，这里设置默认超时时间为10秒
     * @return 返回Promise
     */
    static warp_fetch(fetch_promise, timeout = 10000) {
        let timeout_fn = null;
        let abort = null;
        //创建一个超时promise
        let timeout_promise = new Promise(function (resolve, reject) {
            timeout_fn = function () {
                reject('网络请求超时');
            };
        });
        //创建一个终止promise
        let abort_promise = new Promise(function (resolve, reject) {
            abort = function () {
                reject('请求终止');
            };
        });
        //竞赛
        let abortable_promise = Promise.race([
            fetch_promise,
            timeout_promise,
            abort_promise,
        ]);
        //计时
        setTimeout(timeout_fn, timeout);
        //终止
        abortable_promise.abort = abort;
        return abortable_promise;
    }
}

export default NetUtil;
```

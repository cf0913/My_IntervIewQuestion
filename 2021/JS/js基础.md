# JS基础
1. 什么是堆？什么是栈？它们之间有什么区别和联系
```js
堆和栈的概念存在于数据结构中和操作系统内存中。
在数据结构中，栈中数据的存取方式为先进后出。
而堆是一个优先队列，是按优先级来进行排序的，优先级可以按照大小来规定。完全二叉树是堆的一种实现方式。

在操作系统中，内存被分为栈区和堆区
栈区内存由编译器自动分配释放，存放函数的参数值，局部变量的值等。其操作方式类似于数据结构中的栈
堆区内存一般由程序员分配释放，若程序员不释放，程序结束时可能由垃圾回收机制回收
```
---
2. this的指向和函数的调用模式
```js
函数调用
  this 指向全局，即 window
方法调用
  this 指向调用这个方法的对象
构造器调用
  this 指向刚 new 出来的对象
上下文调用（自定义this，call， apply）
  this 指向定义的对象
```
---
3. 数据类型及判断方法
```js
Number，String，Boolean，Array，Object，undefined，null，Function，Symbol，BigInt

typeof
instanceof  用于检测构造函数的 prototype 属性是否出现在某个实例对象的原型链上
```
---
4. 原型链
```js
什么是原型链
  - 实例对象的constructor是一个构造函数
  - 其可以new出一个对象，称为实例
  - 实例可以通过一个非标准属性__proto__访问其原型对象
  - 构造函数的prototype也是指向这个原型对象
  - 原型对象也可以一直访问其原型对象，这就形成了一条链结构
  - 所以，当访问一个对象的属性时，先在其属性上查找，没找到则在其原型对象上查找，依次往下，最终到null

instanceof的原理
  - 例如实例 A
  - A.instanceof 其实就是判断 A 的__proto__的引用地址是不是和 A 的构造函数的引用一致
  - 注意：只要是其原型链上有的，就返回true
  - 所以可以判断是否是Object 或者 Array

new运算符干了什么
  - 创建了一个新对象，其继承了其构造函数的prototype
  - 然后构造函数被执行，this被指定到新实例
  - 如果构造函数本身有返回对象，则new出来的是其返回的结果
  - 如果没有返回，则创建了一个新的对象
```
---
5. 对象的深拷贝
```js
- 投机取巧的 JSON.Stringify
- 循环递归，且继承其prototype
- 堆栈方式
- 把对象当作树，遍历树

// 浅拷贝的实现;

function shallowCopy(object) {
  // 只拷贝对象
  if (!object || typeof object !== "object") return;

  // 根据 object 的类型判断是新建一个数组还是对象
  let newObject = Array.isArray(object) ? [] : {};

  // 遍历 object，并且判断是 object 的属性才拷贝
  for (let key in object) {
    if (object.hasOwnProperty(key)) {
      newObject[key] = object[key];
    }
  }

  return newObject;
}

// 深拷贝的实现;

function deepCopy(object) {
  if (!object || typeof object !== "object") return;

  let newObject = Array.isArray(object) ? [] : {};

  for (let key in object) {
    if (object.hasOwnProperty(key)) {
      newObject[key] =
        typeof object[key] === "object" ? deepCopy(object[key]) : object[key];
    }
  }

  return newObject;
}
```
---
6. AMD和CMD标准，CommonJS和ES Module
```js
AMD和CMD标准
  - AMD: 提前执行（异步加载：依赖先执行）+ 延迟执行, 例如 RequireJs
  - CMD: 延迟执行（运行到需加载，根据顺序执行）, 例如 SeaJs
CommonJS
  - 可以写在任何位置
  - 可以使用条件语句
  - 输出的值可以改变
  - 运行时加载，无法静态化
ES Module
  - 通过export命令显式指定输出的代码，再通过import命令输入
  - ES6 模块是编译时加载，使得静态分析成为可能。可以进一步拓宽 JavaScript 的语法
  - 静态化，必须在顶部，不能使用条件语句，自动采用严格模式
  - treeshaking和编译优化，以及webpack3中的作用域提升
  - 外部可以拿到实时值，而非缓存值(是引用而不是copy)
差异
  - CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用
  - CommonJS 模块是运行时加载，ES6 模块是编译时输出接口
  - CommonJS是对模块的浅拷贝，ES6 Module是对模块的引用
  - 可以对commonJS对重新赋值（改变指针指向），但是对ES6 Module赋值会编译报错
```
---
7. 事件冒泡和事件捕获
```js
事件捕获
  - 一层一层往下传递，到达目标元素 window -> document -> html -> body -> ...
事件冒泡
  - 从目标元素一层层往上传递，直到window
addEventListener的第三个参数
  - 默认值是false，表示在事件冒泡阶段调用事件处理函数;
  - 如果参数为true，则表示在事件捕获阶段调用处理函数
执行顺序
  - 对于非target节点则先执行捕获在执行冒泡
  - 对于target节点则是先执行先注册的事件，无论冒泡还是捕获
```
---
8. 前端路由的实现方式
```js
HTML5的history
  - 两个Api
    - history.pushState
    - history.replaceState
  - 特点
    - 这两个 API 都会操作浏览器的历史记录，而不会引起页面的刷新

Hash
  - hashchange事件用 window.location 处理哈希的改变时不会重新渲染页面
  - 而是当作新页面加到历史记录中，这样跳转页面就可以在 hashchange 事件中注册 ajax 从而改变页面内容
```
---
9. js 有哪些内置对象
```
值属性，这些全局属性返回一个简单值，这些值没有自己的属性和方法
  - 例如 Infinity、NaN、undefined、null 字面量
函数属性，全局函数可以直接调用，不需要在调用时指定所属对象，执行结束后会将结果直接返回给调用者
  - eval()、parseFloat()、parseInt() 等
基本对象，基本对象是定义或使用其他对象的基础。基本对象包括一般对象、函数对象和错误对象
  - Object、Function、Boolean、Symbol、Error 等
数字和日期对象，用来表示数字、日期和执行数学计算的对象。
  - Number、Math、Date
字符串，用来表示和操作字符串的对象
  - String、RegExp
可索引的集合对象，这些对象表示按照索引值来排序的数据集合，包括数组和类型数组，以及类数组结构的对象。
  - 例如 Array
使用键的集合对象，这些集合对象在存储数据时会使用到键，支持按照插入顺序来迭代元素
  - Map、Set、WeakMap、WeakSet
结构化数据，这些对象用来表示和操作结构化的缓冲区数据，或使用 JSON 编码的数据
  - JSON
控制抽象对象
  - Promise、Generator 等
```
---
10. isNaN 和 Number.isNaN 函数的区别
```
函数 isNaN 接收参数后，会尝试将这个参数转换为数值，任何不能被转换为数值的的值都会返回 true，
因此非数字值传入也会返回 true ，会影响 NaN 的判断。

函数 Number.isNaN 会首先判断传入参数是否为数字，如果是数字再继续判断是否为 NaN 
这种方法对于 NaN 的判断更为准确。
```
---
11. == 操作符的强制类型转换规则
```
1. 字符串和数字之间的相等比较，将字符串转换为数字之后再进行比较。

2. 其他类型和布尔类型之间的相等比较，先将布尔值转换为数字后，再应用其他规则进行比较。

3. null 和 undefined 之间的相等比较，结果为真。其他值和它们进行比较都返回假值。

4. 对象和非对象之间的相等比较，对象先调用 ToPrimitive 抽象操作后，再进行比较。

5. 如果一个操作值为 NaN ，则相等比较返回 false（ NaN 本身也不等于 NaN ）。

6. 如果两个操作值都是对象，则比较它们是不是指向同一个对象。如果两个操作数都指向同一个对象，则相等操作符返回 true，否则，返回 false。
```
---
12. 常用的正则
```
// （1）匹配 16 进制颜色值
var regex = /#([0-9a-fA-F]{6}|[0-9a-fA-F]{3})/g;

// （2）匹配日期，如 yyyy-mm-dd 格式
var regex = /^[0-9]{4}-(0[1-9]|1[0-2])-(0[1-9]|[12][0-9]|3[01])$/;

// （3）匹配 qq 号
var regex = /^[1-9][0-9]{4,10}$/g;

// （4）手机号码正则
var regex = /^1[34578]\d{9}$/g;

// （5）用户名正则
var regex = /^[a-zA-Z\$][a-zA-Z0-9_\$]{4,16}$/;

//  (6) 千分位
number.replace(/(?!^)(?=(\d{3})+\.)/g, ",");
```
---
13. javascript 创建对象的几种方式
```
1）第一种是工厂模式
  工厂模式的主要工作原理是用函数来封装创建对象的细节，从而通过调用函数来达到复用的目的。但是它有一个很大的问题就是创建出来的对象无法和某个类型联系起来，它只是简单的封装了复用代码，而没有建立起对象和类型间的关系。

（2）第二种是构造函数模式
  js 中每一个函数都可以作为构造函数，只要一个函数是通过 new 来调用的，那么我们就可以把它称为构造函数。执行构造函数首先会创建一个对象，然后将对象的原型指向构造函数的 prototype 属性，然后将执行上下文中的 this 指向这个对象，最后再执行整个函数，如果返回值不是对象，则返回新建的对象。因为 this 的值指向了新建的对象，因此我们可以使用 this 给对象赋值。构造函数模式相对于工厂模式的优点是，所创建的对象和构造函数建立起了联系，因此我们可以通过原型来识别对象的类型。但是构造函数存在一个缺点就是，造成了不必要的函数对象的创建，因为在 js 中函数也是一个对象，因此如果对象属性中如果包含函数的话，那么每次我们都会新建一个函数对象，浪费了不必要的内存空间，因为函数是所有的实例都可以通用的。

（3）第三种模式是原型模式
  因为每一个函数都有一个 prototype 属性，这个属性是一个对象，它包含了通过构造函数创建的所有实例都能共享的属性和方法。因此我们可以使用原型对象来添加公用属性和方法，从而实现代码的复用。这种方式相对于构造函数模式来说，解决了函数对象的复用问题。但是这种模式也存在一些问题，一个是没有办法通过传入参数来初始化值，另一个是如果存在一个引用类型如 Array 这样的值，那么所有的实例将共享一个对象，一个实例对引用类型值的改变会影响所有的实例。

（4）第四种模式是组合使用构造函数模式和原型模式
  这是创建自定义类型的最常见方式。因为构造函数模式和原型模式分开使用都存在一些问题，因此我们可以组合使用这两种模式，通过构造函数来初始化对象的属性，通过原型对象来实现函数方法的复用。这种方法很好的解决了两种模式单独使用时的缺点，但是有一点不足的就是，因为使用了两种不同的模式，所以对于代码的封装性不够好。

（5）第五种模式是动态原型模式
  这一种模式将原型方法赋值的创建过程移动到了构造函数的内部，通过对属性是否存在的判断，可以实现仅在第一次调用函数时对原型对象赋值一次的效果。这一种方式很好地对上面的混合模式进行了封装。

（6）第六种模式是寄生构造函数模式
  这一种模式和工厂模式的实现基本相同，我对这个模式的理解是，它主要是基于一个已有的类型，在实例化时对实例化的对象进行扩展。这样既不用修改原来的构造函数，也达到了扩展对象的目的。它的一个缺点和工厂模式一样，无法实现对象的识别。
```
---
14. 闭包
```
闭包是指有权访问另一个函数作用域中变量的函数，创建闭包的最常见的方式就是在一个函数内创建另一个函数，创建的函数可以
访问到当前函数的局部变量。

闭包有两个常用的用途。

闭包的第一个用途是使我们在函数外部能够访问到函数内部的变量。通过使用闭包，我们可以通过在外部调用闭包函数，从而在外
部访问到函数内部的变量，可以使用这种方法来创建私有变量。

函数的另一个用途是使已经运行结束的函数上下文中的变量对象继续留在内存中，因为闭包函数保留了这个变量对象的引用，所以
这个变量对象不会被回收。

其实闭包的本质就是作用域链的一个特殊的应用，只要了解了作用域链的创建过程，就能够理解闭包的实现原理。
```
---
15. 内存泄漏
```js
哪些操作会造成内存泄漏
  1.意外的全局变量
  2.被遗忘的计时器或回调函数
  3.脱离 DOM 的引用
  4.闭包

怎么解决
  1.尽量少用全局变量
  2.清除定时器和解绑函数
  3.已删除的元素要取消对其的引用
  4.不实用不合理的闭包
```
---
16. 如何判断当前环境是window还是node
```js
this === window ? 'browser' : 'node';
```
---
17.实现一个setTimeOut和setInterval
```js
// setTimeOut
function myTimeOut(fn, time) {
  var startTime = Date.now();
  var nowTime = Date.now();

  while(nowTime - startTime < time * 1000) {
    nowTime = Date.now();
  }

  fn();

  return Math.random();
}
// setInterval
function mySetInterval(fn, millisec, count){
  function interval(){
    if(typeof count===‘undefined’||count-->0){
      setTimeout(interval, millisec);
      try{
        fn()
      }catch(e){
        count = 0;
        throw e.toString();
      }
    }
  }
  setTimeout(interval, millisec)
}
```
---
18. 实现一个 new 
```js
function myNew(fn) {
  if (typeof fn !== 'function') {
    throw 'params show be function';
  }
  myNew.target = fn;
  var newObj = Object.create(fn.prototype);
  var argsArr = [].prototype.slice.call(arguments, 1);
  var res = fn.apply(newObj, argsArr);
  var isObject = res && typeof res === 'object';
  var isFunction = typeof res === 'function';
  if (isObj || isFunction) return res;
  return newObj;
}
```
---


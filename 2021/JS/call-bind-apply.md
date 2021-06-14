# call, bind, apply
#### 1.实现一个call
```js
// call函数实现
Function.prototype.myCall = function (context) {
  var context = context || window;  // 兼容传入的null
  context.fn = this;

  var args = [];
  for(var i = 1, len = arguments.length; i < len; i++) {
    args.push('arguments[' + i + ']');
  }
  // 这里也可以用ES6的... , 但是call是ES3的方法，所以用eval
  // var result = eval(context.fn(...args));
  var result = eval('context.fn(' + args +')');

  delete context.fn
  return result;
};
```
---
#### 2.实现一个bind
```js
// bind 函数实现
Function.prototype.myBind = function(context) {
  // 判断调用对象是否为函数
  if (typeof this !== "function") {
    throw new TypeError("Error");
  }

  // 获取参数
  var args = [...arguments].slice(1);
  var fn = this;

  return function Fn() {
    // 根据调用方式，传入不同绑定值
    return fn.apply(
      this instanceof Fn ? this : context,
      args.concat(...arguments)
    );
  };
};
```
---
#### 3.实现一个apply
```js
// apply 函数实现, 和 call类似
Function.prototype.apply = function (context, arr) {
  var context = Object(context) || window;
  context.fn = this;

  var result;
  if (!arr) {
    result = context.fn();
  } else {
    var args = [];
    for (var i = 0, len = arr.length; i < len; i++) {
      args.push('arr[' + i + ']');
    }
    result = eval('context.fn(' + args + ')')
  }
  delete context.fn
  return result;
};
```
---
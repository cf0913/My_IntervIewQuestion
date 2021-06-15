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
Function.prototype.myBind = function(context, ...args){
  // 获取自身的this，也就是 bar
  let self = this;
  let targetFuc =  function(){
    // 获取绑定的函数的传参，也就是 '18'
    let targetArg = Array.prototype.slice.call(arguments)
    // 以例子来看就是将bar的this指向context，也就是fo
    // 正常来说是 self.apply(context,args.concat(targetArg)) 就行了
    // 但是因为bind的返回可以用new绑定，new会改变this指向：
    // 如果bind绑定后的函数被new了，那么this指向会发生改变，指向当前函数的实例。也就是targetFuc
    // this instanceof targetFuc 判断返回的函数是否被new绑定在了实例上
    // 因为bind()除了this还接收其他参数，bind()返回的函数也接收参数，这两部分的参数都要传给返回的函数
    // args.concat(targetArg) 就相当于 ['daisy'].concat(['18])
    return self.apply(this instanceof targetFuc ? this: context, args.concat(targetArg))
  }
  // 修改返回函数的 prototype 为绑定函数的 prototype，实例就可以继承绑定函数的原型中的值，也就是friend和habit
  targetFuc.prototype = Object.create(self.prototype);
  return targetFuc;
}
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
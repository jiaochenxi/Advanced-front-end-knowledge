# 实现api（中）

#### 通过 Koa only 模块源码认识 reduce {#koaonlyreduce}

only的用法：

```
var o = {
    a: 'a',
    b: 'b',
    c: 'c'
}
only(o, ['a','b'])   // {a: 'a',  b: 'b'}
```

实现方法：

```
var only = function(obj, keys){
    obj = obj || {}
    if ('string' == typeof keys) keys = keys.split(/ +/)
    return keys.reduce(function(ret, key) {
        if (null == obj[key]) return ret
        ret[key] = obj[key]
        return ret
    }, {})
}
```

### compose 实现的几种方案 {#fontcolor13aa6ccomposefont}

`compose`其实和前面提到的`pipe`一样，就是执行一连串不定长度的任务（方法），比如：

```
let funcs = [fn1, fn2, fn3, fn4]
let composeFunc = compose(...funcs)
composeFunc(args)
```

相当于

```
fn1(fn2(fn3(fn4(args))))
```

总结一下`compose`方法的关键点：

* `compose`的参数是函数数组，返回的也是一个函数
* `compose`的参数是任意长度的，所有的参数都是函数，执行方向是自右向左的，因此初始函数一定放到参数的最右面
* `compose`执行后返回的函数可以接收参数，这个参数将作为初始函数的参数，所以初始函数的参数是多元的，初始函数的返回结果将作为下一个函数的参数，以此类推。因此除了初始函数之外，其他函数的接收值是一元的

`compose`和`pipe`的差别只在于调用顺序的不同：

```
// compose
fn1(fn2(fn3(fn4(args))))

// pipe
fn4(fn3(fn2(fn1(args))))
```

实现的方法：

```
const compose = function(...args) {
    let length = args.length
    let count = length - 1
    let result
    return function f1 (...arg1) {
        result = args[count].apply(this, arg1)
        if (count <= 0) {
            count = length - 1
            return result
        }
        count--
        return f1.call(null, result)
    }
}
```

`reduce`能更**函数式**地解决问题（reverse数组颠倒）

```
const reduceFunc = (f, g) => (...arg) => g.call(this, f.apply(this, arg))
const compose = (...args) => args.reverse().reduce(reduceFunc, args.shift())
//shift() 方法用于把数组的第一个元素从其中删除，并返回第一个元素的值。
```

**我们继续开拓思路，“既然涉及串联和流程控制”，那么还可以使用 Promise 实现：**

```
const compose = (...args) => {
    let init = args.pop()
    return (...arg) => 
    args.reverse().reduce((sequence, func) => 
      sequence.then(result => func.call(null, result))
    , Promise.resolve(init.apply(null, arg)))
}
```

这种实现利用了 Promise 特性：首先通过`Promise.resolve(init.apply(null, arg))`启动逻辑，启动一个`resolve`值为最后一个函数接收参数后的返回值，依次执行函数。因为`promise.then()`仍然返回一个 Promise 类型值，所以`reduce`完全可以按照 Promise 实例执行下去。

**lodash 版本**

```
// lodash 版本
var compose = function(funcs) {
    var length = funcs.length
    var index = length
    while (index--) {
        if (typeof funcs[index] !== 'function') {
            throw new TypeError('Expected a function');
        }
    }
    return function(...args) {
        var index = 0
        var result = length ? funcs.reverse()[index].apply(this, args) : args[0]
        while (++index < length) {
            result = funcs[index].call(this, result)
        }
        return result
    }
}
```

**Redux 版本**

```
// Redux 版本
function compose(...funcs) {
    if (funcs.length === 0) {
        return arg => arg
    }

    if (funcs.length === 1) {
        return funcs[0]
    }

    return funcs.reduce((a, b) => (...args) => a(b(...args)))
}
```

### apply、bind 进阶实现 {#fontcolor13aa6capplybindfont}

bind的实现（参考this）

```
Function.prototype.bind = Function.prototype.bind || function (context) {
    var me = this;
    var args = Array.prototype.slice.call(arguments, 1);
    return function () {
        var innerArgs = Array.prototype.slice.call(arguments);
        var finalArgs = args.concat(innerArgs);
        return me.apply(context, finalArgs);
    }
}
```

**先简要解读一下：**

基本原理是使用`apply`进行模拟`bind`。函数体内的`this`就是需要绑定`this`的函数，或者说是原函数。最后使用`apply`来进行参数（`context`）绑定，并返回。

与此同时，将第一个参数（`context`）以外的其他参数，作为提供给原函数的预设参数，这也是基本的“ curry 化”基础。

上述实现方式，我们返回的参数列表里包含：`argsArray.slice(1)`，**它的问题在于存在预置参数功能丢失的现象。**

真正实现“ curry 化”的“完美方式”是：

```
Function.prototype.bind = Function.prototype.bind || function (context) {
    var me = this;
    var args = Array.prototype.slice.call(arguments, 1);
    return function () {
        var innerArgs = Array.prototype.slice.call(arguments);
        var finalArgs = args.concat(innerArgs);
        return me.apply(context, finalArgs);
    }
}
```

但继续探究，我们注意`bind`方法中：`bind`返回的函数如果作为构造函数，搭配`new`关键字出现的话，我们的绑定`this`就需要“被忽略”，`this`要绑定在实例上。也就是说，`new`的操作符要高于`bind`绑定，兼容这种情况的实现：

```
Function.prototype.bind = Function.prototype.bind || function (context) {
    var me = this;
    var args = Array.prototype.slice.call(arguments, 1);
    var F = function () {};
    F.prototype = this.prototype;
    var bound = function () {
        var innerArgs = Array.prototype.slice.call(arguments);
        var finalArgs = args.concat(innerArgs);
        return me.apply(this instanceof F ? this : context || this, finalArgs);
    }
    bound.prototype = new F();
    return bound;
}
```




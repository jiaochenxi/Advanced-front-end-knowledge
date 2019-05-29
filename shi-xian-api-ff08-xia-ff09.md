# 实现api（下）

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




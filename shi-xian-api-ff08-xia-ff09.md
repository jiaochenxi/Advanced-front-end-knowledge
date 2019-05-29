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


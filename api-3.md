### 实现 Api（下）

es5-shim实现bind方法

```
function bind(that) {
    var target = this;
    if (!isCallable(target)) {
        throw new TypeError('Function.prototype.bind called on incompatible ' + target);
    }
    var args = array_slice.call(arguments, 1);
    var bound;
    var binder = function () {
        if (this instanceof bound) {
            var result = target.apply(
                this,
                array_concat.call(args, array_slice.call(arguments))
            );
            if ($Object(result) === result) {
                return result;
            }
            return this;
        } else {
            return target.apply(
                that,
                array_concat.call(args, array_slice.call(arguments))
            );
        }
    };
    var boundLength = max(0, target.length - args.length);
    var boundArgs = [];
    for (var i = 0; i < boundLength; i++) {
        array_push.call(boundArgs, '$' + i);
    }
    bound = Function('binder', 'return function (' + boundArgs.join(',') + '){ return binder.apply(this, arguments); }')(binder);

    if (target.prototype) {
        Empty.prototype = target.prototype;
        bound.prototype = new Empty();
        Empty.prototype = null;
    }
    return bound;
}
```

**es5-shim 是为了最大限度地进行兼容，包括对返回函数**`length`**属性的还原**。

### 实现`call`/`apply`

```
Function.prototype.applyFn = function (targetObject, argsArray) {
    if(typeof argsArray === 'undefined' || argsArray === null) {
        argsArray = []
    }

    if(typeof targetObject === 'undefined' || targetObject === null){
        targetObject = this
    }

    targetObject = new Object(targetObject)

    const targetFnKey = 'targetFnKey'
    targetObject[targetFnKey] = this

    const result = targetObject[targetFnKey](...argsArray)
    delete targetObject[targetFnKey]
    return result
}
```

如果`targetObject`对象本身就存在`targetFnKey`这样的属性，那么在使用`applyFn`函数时，原有的`targetFnKey`属性值就会被覆盖，之后被删除。解决方案可以使用 ES6`Sybmol()`来保证键的唯一性；另一种解决方案是用`Math.random()`实现独一无二的 key


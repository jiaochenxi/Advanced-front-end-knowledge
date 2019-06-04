# 其他基础题库（2）

### cannot read property of undefined 问题解决方案 {#fontcolor13aa6ccannotreadpropertyofundefinedfont}

如：

```
const obj = {
    user: {
        posts: [
            { title: 'Foo', comments: [ 'Good one!', 'Interesting...' ] },
            { title: 'Bar', comments: [ 'Ok' ] },
            { title: 'Baz', comments: []}
        ],
        comments: []
    }
}
```

为了在对象中相关取值的过程，需要验证对象每一个 key 的存在性。常见的处理方案有：

###### &&短路运算符进行可访问性嗅探

```
obj.user &&
obj.user.posts &&
obj.user.posts[0] &&
obj.user.posts[0].comments
```

###### \|\|单元设置默认保底值

```
(((obj.user || {}).posts||{})[0]||{}).comments
```

###### try...catch

```
var result
try {
    result = obj.user.posts[0].comments
}
catch {
    result = null
}
```

###### lodash 等库 get API

自己实现的方法：

```
const get = (p, o) => p.reduce((xs, x) => (xs && xs[x]) ? xs[x] : null, o)

console.log(get(['user', 'posts', 0, 'comments'], obj)) // [ 'Good one!', 'Interesting...' ]
console.log(get(['user', 'post', 0, 'comments'], obj)) // null
```

curry 化方法：

```
const get = p => o =>
    p.reduce((xs, x) =>
        (xs && xs[x]) ? xs[x] : null, o)

const getUserComments = get(['user', 'posts', 0, 'comments'])

console.log(getUserComments(obj))
// [ 'Good one!', 'Interesting...' ]
console.log(getUserComments({user:{posts: []}}))
// null
```

TC39 提案中有一个新的提案

```
console.log(obj?.user?.posts[0]?.comments)
```

### 分析一道网红题目 {#fontcolor13aa6cfont}

Can \(a == 1&&a == 2&&a == 3\) ever evaluate to true?

方案一：

```
const a = {
    value: 1,
    toString: function () {
        return a.value++
    }
}
console.log(a == 1 && a == 2 && a == 3) // true
```

这个方案中，我们将 a 定义为一个对象，并重写了其 toString 方法。因此在每次进行判断时，按照规则，== 号两边出现了对象类型，另一边是 Number 类型，需要调用 a 对象 toString 方法，toString 方法的返回值会作为对象转为基本类型的值，我们每次将 value 属性加 1。同样，如果按照相同的方式重写 valueOf 方法，也是可以达到同样目的的。

方案二：

```
let value = 0
Object.defineProperty(window, 'a', {
    get: function() {
        return ++value
    }
})

console.log(a == 1 && a == 2 && a == 3) // true
```

这里我们同样将 a 定义成对象，并且采用了 Object.defineProperty 的方式，重写了其 getter 方法。

### type.js 源码解读 {#fontcolor13aa6ctypejsfont}

[type.js](https://github.com/jsmini/type)是由颜海镜编写的用于判断数据类型的方法库，其兼容 IE6，灵活运用了多种判断类型方式

```
const toString = Object.prototype.toString;

export function type(x, strict = false) {
    strict = !!strict;

    // fix typeof null = object
    if(x === null){
        return 'null';
    }

    const t = typeof x;

    // number string boolean undefined symbol
    if(t !== 'object'){
        return t;
    }

    let cls;
    let clsLow;
    try {
        cls = toString.call(x).slice(8, -1);
        clsLow = cls.toLowerCase();
    } catch(e) {
        // ie 下的 activex 对象
        return 'object';
    }

    if(clsLow !== 'object'){
        // 区分 String() 和 new String()
        if (strict && (clsLow === 'number' || clsLow === 'boolean' || clsLow === 'string')) {
            return cls;
        }
        return clsLow;
    }

    if(x.constructor == Object){
        return clsLow;
    }

    // Object.create(null)
    try {
        // __proto__ 部分早期 firefox 浏览器
        if (Object.getPrototypeOf(x) === null || x.__proto__ === null) {
            return 'object';
        }
    } catch(e) {
        // ie下无 Object.getPrototypeOf 会报错
    }

    // function A() {}; new A
    try {
        const cname = x.constructor.name;

        if (typeof cname === 'string') {
            return cname;
        }
    } catch(e) {
        // 无 constructor
    }

    // function A() {}; A.prototype.constructor = null; new A
    return 'unknown';
}
```

其中关键点提炼出来有：

* 通过 x === null 来判断 null 类型
* 对于 typeof x 不为 object 的情况，直接返回 typeof x 结果，这时候可以判断出 number，string，boolean，undefined，symbol 类型
* 其他情况，对于 IE6 以上版本，使用 Object.prototype.toString 方法并进行返回
* 兼容性处理，比如对于不支持 Object.prototype.toString 方法的情况，返回 object
* 其他兼容性处理

对返回结果使用 .slice\(8, -1\)，更加方便拿到结果：




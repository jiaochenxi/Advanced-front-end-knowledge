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




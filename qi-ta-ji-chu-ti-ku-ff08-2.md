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




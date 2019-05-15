# 闭包（下）

示例：

```
const foo = () => {
    var arr = []
    var i

    for (i = 0; i < 10; i++) {
        arr[i] = function () {
            console.log(i)
        }
    }

    return arr[0]
}

foo()()//10
```

分析类似例题 1：`foo()`执行返回的是`arr[0]`,`arr[0]`此时是函数：

```
function () {
    console.log(i)
}
```

```
var fn = null
const foo = () => {
    var a = 2
    function innerFoo() { 
        console.log(a)
    }
    fn = innerFoo    
}

const bar = () => {
    fn()
}

foo()
bar()
```

答案是2。正常来讲，根据调用栈的知识，`foo`函数执行完毕之后，其执行环境生命周期会结束，所占内存被垃圾收集器释放，上下文消失。但是通过`innerFoo`函数赋值给`fn`，`fn`是全局变量，这就导致了`foo`的变量对象`a`也被保留了下来。所以函数`fn`在函数`bar`内部执行时，依然可以访问这个被保留下来的变量对象，输出结果为`2`。

```
var fn = null
const foo = () => {
    var a = 2
    function innerFoo() { 
        console.log(c)            
        console.log(a)
    }
    fn = innerFoo
}

const bar = () => {
    var c = 100
    fn()    
}

foo()
bar()
```

在`bar`中执行`fn()`时，`fn()`已经被复制为`innerFoo`，变量`c`并不在其作用域链上，`c`只是`bar`函数的内部变量。因此报错 ReferenceError: c is not defined。

![](/assets/import.png)

```
function Person() {
    this.name = 'lucas'
}

const getSingleInstance = (function(){
     var singleInstance
    return function() {
         if (singleInstance) {
            return singleInstance
         } 
        return singleInstance = new Person()
    }
})()

const instance1 = new getSingleInstance()
const instance2 = new getSingleInstance()
```

事实上，我们有 instance1 === instance2。因为借助闭包变量 singleInstance，instance1 和 instance2 是同一引用的（singleInstance），这正是单例模式的体现。






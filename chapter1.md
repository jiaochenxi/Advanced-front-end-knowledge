# This

#### **1、This到底指向谁：**

#### 谁调用它，this就指向谁

###### this 的指向，是在调用函数时根据执行上下文所动态决定的

* 在函数体中，简单调用该函数时（非显式／隐式绑定下），严格模式下this绑定到undefined，否则绑定到全局对象window／global；
* 一般构造函数new调用，绑定到新创建的对象上；
* 一般由call／apply／bind方法显示调用，绑定到指定参数的对象上
* 一般由上下文对象调用，绑定在该对象上
* 箭头函数中，根据外层上下文绑定的this决定this指向

#### 全局环境下的this：

```
function f1 () { 
    console.log(this) 
} 
function f2 () { 
 'use strict’ //指明严格模式
 console.log(this) 
} 
f1() // window 
f2() // undefined
```

###### 难点：

```
const foo = { 
    bar: 10, 
    fn: function() { 
        console.log(this) 
        console.log(this.bar) 
    } 
} 
var fn1 = foo.fn 
fn1()
```

这里this指的是window

```
const foo = {
    bar: 10,
    fn: function() {
        console.log(this)
        console.log(this.bar)
    }
}
foo.fn()
```

这里this指的就是foo

###### 在执行函数时，如果函数中this是被上一级的对象所调用，那么 this 指向的是上一级的对象；否则指向全局环境

#### 上下文对象调用中的this：

```
const o1 = {
    text: 'o1’,

    fn: function\(\) {

        return this.text 

    } 
}
const o2 = {
    text: 'o2’, 

    fn: function\(\) { 

        return o1.fn\(\) 

    } 
}
const o3 = {
    text: 'o3’, 

    fn: function\(\) { 

        var fn = o1.fn 

        return fn\(\) 

    } 
}
const o4 = {
        text: 'o4’,

        fn: o1.fn
}

console.log(o1.fn()) //o1
console.log(o2.fn()) //o1
console.log(o3.fn()) //undefined
console.log(o4.fn()) //o4
```

#### bind／call／apply 改变this指向

通过在call方法，给第一个参数添加要把b添加到哪个环境中，简单来说，this就会指向那个对象。

```
var a = {
    user:"追梦子",

    fn:function\(\){

        console.log\(this.user\); //追梦子

    }
}
var b = a.fn;
b.call(a);
```

**Call**： 可以添加多个参数，如：

```
var a = {
    user:"追梦子",

    fn:function\(e,ee\){

        console.log\(this.user\); //追梦子

        console.log\(e+ee\); //3

    }
}
var b = a.fn;
b.call(a,1,2);
```

**Apply**: 和call类似，但是第二个参数必须是一个数组

```
var a = {
user:"追梦子",

fn:function\(e,ee\){

    console.log\(this.user\); //追梦子

    console.log\(e+ee\); //11

}
}
var b = a.fn;
b.apply(a,[10,1]);
```

注意如果call和apply的第一个参数写的是null，那么this指向的是window对象

**bind：**bind方法返回的是一个修改过后的函数

```
var a = {
user:"追梦子",

fn:function\(\){

    console.log\(this.user\);

}
}
var b = a.fn;
var c = b.bind(a);
console.log(c); //function() { [native code] }
c();//
```

同样bind也可以有多个参数，并且参数可以执行的时候再次添加，但是要注意的是，参数是按照形参的顺序进行的

```
var a = {
user:"追梦子",

fn:function\(e,d,f\){

    console.log\(this.user\); //追梦子

    console.log\(e,d,f\); //10 1 2

}
}
var b = a.fn;
var c = b.bind(a,10);
c(1,2);
```

举个例子

```
const foo = {
name: ’name1’,

logName: function\(\){

    console.log\(this.name\)

}
}
const bar = {
name: 'bar'
}
console.log(foo.logName.call(bar))//mike
```

#### 构造函数和this：

new操作符调用构造函数：

* 创建一个新的对象
* 将构造函数的this指向新的对象
* 为这个对象添加属性、方法
* 最终返回新对象

new Foo\(\)相当于

```
var obj = {}
Obj.__protp__ = Foo.prototype
Foo.call(obj);
```

如果构造函数中显示返回一个值，且返回的是一个对象，那么this就指向这个返回的对象；如果返回的不是一个对象，this仍然指向实例

#### 箭头函数和this：

箭头函数使用this不适用以上标准规则，而是根据外层（函数或者全局）上下文来决定的；

```
const foo = {
    fn:function(){
        setTimeout(function(){
            console.log(this);
        })
    }
}
console.log(foo.fn());//this指的是window

```

```
const foo = {
    fn:function(){
        setTimeout(() => {
            console.log(this);
        })
    }
}
console.log(foo.fn());//this指的是foo

```

#### this优先级：

显式绑定：call，apply，bind，new

隐式绑定：根据调用关系确定this指向

显示绑定高于隐式绑定，new比bind更高，箭头函数的绑定无法修改

### 3、开放例题分析：

```
Function.prototype.bind = Function.prototype.bind ||
function(context) {
var me = this;

var args = Array.prototype.slice.call\(arguments, 1\);

return function bound\(\) {

    var innerArgs = Array.prototype.slice.call\(arguments\);

    var finalArgs = args.concat\(innerArgs\);

    return me.apply\(context, finalArgs\);

}
}
```

但是，bind返回的函数如果作为构造函数，搭配new关键字出现的话，我们绑定的this，就需要“被忽略”

Ps:

arguments是一个对应于传递给函数的参数的类数组对象。

arguments对象是所有（非箭头）函数中都可用的局部变量。你可以使用arguments对象在函数中引用函数的参数

arguments对象不是一个[Array](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Array)。它类似于Array，但除了length属性和索引元素之外没有任何Array属性，但是它可以被转换为一个真正的Array，如

```
var args = Array.prototype.slice.call(arguments);
```




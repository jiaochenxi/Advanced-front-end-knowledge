# 其他基础题库

![](/assets/1-6.png)

### JavaScript 类型及其判断 {#fontcolor13aa6cjavascriptfont}

JavaScript 具有七种内置数据类型，它们分别是：

* null
* undefined
* boolean
* number
* string
* object（包含了function、array、date等）
* symbol

对于这些类型的判断，我们常用的方法有：

* typeof
* instanceof
* Object.prototype.toString
* constructor

如：

```
typeof null // "object"

const foo = []
typeof foo // "object"
```

使用 typeof 可以准确判断出除 null 以外的基本类型，以及 function 类型、symbol 类型；null 会被 typeof 判断为 object。

#### 使用 instanceof 判断类型 {#instanceof}

**使用 a instanceof B 判断的是：a 是否为 B 的实例，即 a 的原型链上是否存在 B 构造函数**。

```
// L 表示左表达式，R 表示右表达式
const instanceofMock = (L, R) => {
    if (typeof L !== 'object') {
        return false
    }
    while (true) { 
        if (L === null) {
            // 已经遍历到了最顶端
            return false
        }
        if (R.prototype === L.__proto__) {
            return true
        }
        L = L.__proto__
    } 
}
```

```
instanceofMock('', String)

// false

function Person(name) {
    this.name = name
}
const p = new Person('lucas')

instanceofMock(p, Person)

// true
```

#### 使用 constructor 和 Object.prototype.toString 判断类型 {#constructorobjectprototypetostring}

使用 constructor 可以查看目标的构造函数，这也可以进行类型判断

```
console.log(Object.prototype.toString.call(1)) 
// [object Number]

console.log(Object.prototype.toString.call('lucas')) 
// [object String]

console.log(Object.prototype.toString.call(undefined)) 
// [object Undefined]

console.log(Object.prototype.toString.call(true)) 
// [object Boolean]

console.log(Object.prototype.toString.call({})) 
// [object Object]

console.log(Object.prototype.toString.call([])) 
// [object Array]

console.log(Object.prototype.toString.call(function(){})) 
// [object Function]

console.log(Object.prototype.toString.call(null)) 
// [object Null]

console.log(Object.prototype.toString.call(Symbol('lucas'))) 
// [object Symbol]
```

但也存在着问题

```
var foo = 5
foo.constructor
// ƒ Number() { [native code] }

var foo = 'Lucas'
foo.constructor
// ƒ String() { [native code] }

var foo = true
foo.constructor
// ƒ Boolean() { [native code] }

var foo = []
foo.constructor
// ƒ Array() { [native code] }

var foo = {}
foo.constructor
// ƒ Object() { [native code] }

var foo = () => 1
foo.constructor
// ƒ Function() { [native code] }

var foo = new Date()
foo.constructor
// ƒ Date() { [native code] }

var foo = Symbol("foo") 
foo.constructor
// ƒ Symbol() { [native code] }

var foo = undefined
foo.constructor
// VM257:1 Uncaught TypeError: Cannot read property 'constructor' of undefined
    at <anonymous>:1:5

var foo = null
foo.constructor
// VM334:1 Uncaught TypeError: Cannot read property 'constructor' of null
    at <anonymous>:1:5
```

undefined 和 null，如果尝试读取其 constructor 属性，将会进行报错。并且 constructor 返回的是构造函数本身，一般使用它来判断类型的情况并不多见。


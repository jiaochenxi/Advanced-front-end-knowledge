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

### JavaScript 类型及其转换 {#fontcolor13aa6cjavascriptfont-1}

当使用 + 运算符计算 string 和其他类型相加时，都会转换为 string 类型；其他情况，都会转换为 number 类型，但是 undefined 和 null 会转换为 NaN，相加结果也是 NaN。比如布尔值转换为 number 类型：true 为 1，false 为 0。

**结论：**

当使用 + 运算符计算时，如果存在复杂类型，那么复杂类型将会转换为基本类型，再进行运算

规则：对象在转换基本类型时，会调用该对象上 valueOf 或 toString 这两个方法，该方法的返回值是转换为基本类型的结果。如果倾向于转换为 Number 类型，就优先调用 valueOf；如果倾向于转换为 String 类型，就只调用 toString。

```
const foo = {
  toString () {
    return 'lucas'
  },
  valueOf () {
    return 1
  }
}
alert(foo);//lucas
console.log(1 + foo);//2
```

所以：如果加号两边都是 Number 类型，其规则为：

如果 + 号两边存在 NaN，则结果为 NaN（typeof NaN 是 'number）

* 如果是 Infinity + Infinity，结果是 Infinity
* 如果是 -Infinity + \(-Infinity\)，结果是 -Infinity
* 如果是 Infinity + \(-Infinity\)，结果是 NaN

如果加号两边有至少一个是字符串，其规则为：

如果 + 号两边都是字符串，则执行字符串拼接

* 如果 + 号两边只有一个值是字符串，则将另外的值转换为字符串，再执行字符串拼接
* 如果 + 号两边有一个是对象，则调用 valueof\(\) 或者 toStrinig\(\) 方法取得值，转换为基本类型再进行字符串拼接。




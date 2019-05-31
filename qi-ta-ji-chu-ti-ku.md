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




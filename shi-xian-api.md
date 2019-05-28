# 实现api

### jQuery offset 实现 {#fontcolor13aa6cjqueryoffsetfont}

主要有两种思路：

* 通过递归实现
* 通过`getBoundingClientRect`API 实现

#### 递归实现方案

我们通过遍历目标元素、目标元素的父节点、父节点的父节点......依次溯源，并累加这些遍历过的节点相对于其最近祖先节点（且`position`属性非`static`）的偏移量，向上直到`document`，累加即可得到结果。其中，我们需要使用 JavaScript 的`offsetTop`来访问一个 DOM 节点上边框相对离其本身最近、且`position`值为非`static`的祖先元素的垂直偏移量

```
const offset = ele => {
    let result = {
        top: 0,
        left: 0
    }

const getOffset = (node, init) => {
        if (node.nodeType !== 1) {
            return
        }

        position = window.getComputedStyle(node)['position']

        if (typeof(init) === 'undefined' && position === 'static') {
            getOffset(node.parentNode)
            return
        }

        result.top = node.offsetTop + result.top - node.scrollTop
        result.left = node.offsetLeft + result.left - node.scrollLeft

        if (position === 'fixed') {
            return
        }

        getOffset(node.parentNode)
    }

    // 当前 DOM 节点的 display === 'none' 时, 直接返回 {top: 0, left: 0}
    if (window.getComputedStyle(ele)['display'] === 'none') {
        return result
    }

    let position

    getOffset(ele, true)

    return result

}
```

#### getBoundingClientRect 方法 {#getboundingclientrect}

getBoundingClientRect方法用来描述一个元素的具体位置

返回一个对象，bottom,height,left,right,top,width,x,y

![](/assets/db68ca10-4eac-11e9-b1fa-0757868d211c.png)

```
const offset = ele => {
    let result = {
        top: 0,
        left: 0
    }
    // 当前为 IE11 以下，直接返回 {top: 0, left: 0}
    if (!ele.getClientRects().length) {
        return result
    }

    // 当前 DOM 节点的 display === 'none' 时，直接返回 {top: 0, left: 0}
    if (window.getComputedStyle(ele)['display'] === 'none') {
        return result
    }

    result = ele.getBoundingClientRect()
    var docElement = ele.ownerDocument.documentElement

    return {
        top: result.top + window.pageYOffset - docElement.clientTop,
        left: result.left + window.pageXOffset - docElement.clientLeft
    }
}
```

**需要注意的细节有：**

* `node.ownerDocument.documentElement`的用法可能大家比较陌生，`ownerDocument`是 DOM 节点的一个属性，它返回当前节点的顶层的`document`对象。`ownerDocument`是文档，`documentElement`是根节点。事实上，`ownerDocument`下含 2 个节点：

* `<!DocType>`

* `documentElement`

`docElement.clientTop`，`clientTop`是一个元素顶部边框的宽度，不包括顶部外边距或内边距。

* 除此之外，该方法实现就是简单的几何运算，边界 case 和兼容性处理，也并不难理解。

### 数组 reduce 方法的相关实现 {#fontcolor13aa6creducefont}

数组方法非常重要：**因为数组就是数据，数据就是状态，状态反应着视图**。

```
arr.reduce(callback[, initialValue])
```

这里我们简要介绍一下。

* `reduce`第一个参数`callback`是核心，它对数组的每一项进行“叠加加工”，其最后一次返回值将作为`reduce`方法的最终返回值。 它包含 4 个参数：
* `previousValue`表示“上一次”`callback`函数的返回值
* `currentValue`数组遍历中正在处理的元素
* `currentIndex`可选，表示`currentValue`在数组中对应的索引。如果提供了`initialValue`，则起始索引号为 0，否则为 1
* `array`可选，调用`reduce()`的数组
* `initialValue`可选，作为第一次调用`callback`时的第一个参数。如果没有提供`initialValue`，那么数组中的第一个元素将作为`callback`的第一个参数。

![](/assets/reduce.png)

#### reduce 实现 runPromiseInSequence {#reducerunpromiseinsequence}

`runPromiseInSequence`方法将会被一个每一项都返回一个 Promise 的数组调用，并且依次执行数组中的每一个 Promise。




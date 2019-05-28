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

![](/assets/db68ca10-4eac-11e9-b1fa-0757868d211c.png)






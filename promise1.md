# Promise 基本介绍

方法：`resolve`、`reject`、`then`、`catch`

它有以下三种状态：

* `pending`：初始值，不是fulfilled，也不是rejected
* `fulfilled`：代表操作成功
* `rejected`：代表操作失败

注意：`Promise`一旦新建就会「立即执行」，无法取消。这也是它的缺点之一。

类似构建对象，我们使用`new`来构建一个`Promise`。`Promise`接受一个「函数」作为参数，该函数的两个参数分别是`resolve`

和`reject`。


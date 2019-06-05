# Promise 基本介绍

方法：`resolve`、`reject`、`then`、`catch`

它有以下三种状态：

* `pending`：初始值，不是fulfilled，也不是rejected
* `fulfilled`：代表操作成功
* `rejected`：代表操作失败

注意：`Promise`一旦新建就会「立即执行」，无法取消。这也是它的缺点之一。

类似构建对象，我们使用`new`来构建一个`Promise`。`Promise`接受一个「函数」作为参数，该函数的两个参数分别是`resolve`

和`reject`。

`resolve`函数的作用：在异步操作成功时调用，并将异步操作的结果，作为参数传递出去；

`reject`函数的作用：在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。

可以用`then`方法指定`resolved`状态和`reject`状态的回调函数，返回一个Promise

如：

```
//构建Promise
var promise = new Promise(function (resolve, reject) {
    if (/* 异步操作成功 */) {
        resolve(data);
    } else {
        /* 异步操作失败 */
        reject(error);
    }
});
```

```
promise.then(onFulfilled, onRejected);

promise.then(function(data) {
  // do something when success
}, function(error) {
  // do something when failure
});
```

### 基本api

### .then\(\) {#articleHeader10}

对promise添加`onFulfilled`和`onRejected`回调，并返回的是一个新的Promise实例（不是原来那个Promise实例），且返回值将作为参数传入这个新Promise的`resolve`函数。

### .catch\(\) {#articleHeader11}

该方法是`.then(undefined, onRejected)`的别名，用于指定发生错误时的回调函数。

```
promise.then(function(data) {
    console.log('success');
}).catch(function(error) {
    console.log('error', error);
});

/*******等同于*******/
promise.then(function(data) {
    console.log('success');
}).then(undefined, function(error) {
    console.log('error', error);
});
```

```
var promise = new Promise(function (resolve, reject) {
    throw new Error('test');
});
/*******等同于*******/
var promise = new Promise(function (resolve, reject) {
    reject(new Error('test'));
});

//用catch捕获
promise.catch(function (error) {
    console.log(error);
});
-------output-------
Error: test
```

从上例可以看出，

`reject`

方法的作用，等同于抛错。


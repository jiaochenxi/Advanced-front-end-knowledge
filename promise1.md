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

从上例可以看出，`reject`方法的作用，等同于抛错。

promise对象的错误，会一直向后传递，直到被捕获。即错误总会被下一个`catch`所捕获。`then`方法指定的回调函数，若抛出错误，也会被下一个`catch`捕获。`catch`中也能抛错，则需要后面的`catch`来捕获。

promise状态一旦改变就会凝固，不会再改变。因此promise一旦`fulfilled`了，再抛错，也不会变为`rejected`，就不会被`catch`了

```
var promise = new Promise(function(resolve, reject) {
  resolve();
  throw 'error';
});

promise.catch(function(e) {
   console.log(e);      //This is never called
});
```

### .all\(\) {#articleHeader12}

`Promise.all`方法接受一个数组（或具有Iterator接口）作参数，数组中的对象（p1、p2、p3）均为promise实例（如果不是一个promise，该项会被用`Promise.resolve`转换为一个promise\)。它的状态由这三个promise实例决定。

* 当p1, p2, p3状态都变为`fulfilled`，p的状态才会变为`fulfilled`，并将三个promise返回的结果，按参数的顺序（而不是`resolved`的顺序）存入数组，传给p的回调函数。

```
var p1 = new Promise(function (resolve, reject) {
    setTimeout(resolve, 3000, "first");
});
var p2 = new Promise(function (resolve, reject) {
    resolve('second');
});
var p3 = new Promise((resolve, reject) => {
  setTimeout(resolve, 1000, "third");
}); 

Promise.all([p1, p2, p3]).then(function(values) { 
  console.log(values); 
});

-------output-------
//约 3s 后
["first", "second", "third"]
```

* 当p1, p2, p3其中之一状态变为`rejected`，p的状态也会变为`rejected`，并把第一个被`reject`的promise的返回值，传给p的回调函数。

```
var p1 = new Promise((resolve, reject) => { 
  setTimeout(resolve, 1000, "one"); 
}); 
var p2 = new Promise((resolve, reject) => { 
  setTimeout(reject, 2000, "two"); 
});
var p3 = new Promise((resolve, reject) => {
  reject("three");
});

Promise.all([p1, p2, p3]).then(function (value) {
    console.log('resolve', value);
}, function (error) {
    console.log('reject', error);    // => reject three
});

-------output-------
reject three
```

### .race\(\) {#articleHeader13}

该方法同样是将多个Promise实例，包装成一个新的Promise实例。




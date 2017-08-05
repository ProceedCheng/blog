# async函数

```javascript
var fs = require('fs');

var readFile = function (fileName) {
  return new Promise(function (resolve, reject) {
    fs.readFile(fileName, function (error, data) {
      if (error) reject(error);
      resolve(data);
    })
  });
};

var gen = async function () {
  var f1 = await readFile('/etc/fstab');
  var f2 = await readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
}
```

`async`函数对Generator函数的改进，体现在以下四点。

1. 内置执行器

   Generator函数的执行必须靠执行器，所以才有`co`模块，而`async`函数自带执行器。也就是说，`async`函数的执行，与普通函数一模一样，只要一行。

   ```javascript
   var result = asyncReadFile();
   ```

2. 更好的语义

   `async`和`await`，比起`*`和`yield`，语义更清楚了。`async`表示函数里有异步操作，`await`表示紧跟在后面的表达式需要等待结果。

3. 更广的适用性

​       `co`模块约定，`yield`命令后面只能是Thunk函数或Promise对象，而async的`await`命令后面，可以是Promise对象和原始类型的值（数值、字符串和布尔值，但这时等同于同步操作）。

4. 返回值是Promise

   `async`函数的返回值是Promise对象，这比Generator函数的返回值是Iterator对象方便多了。你可以用`then`方法指定下一步的操作。



进一步说，`async`函数完全可以看作多个异步操作，包装成的一个Promise对象，而`await`命令就是内部`then`命令的语法糖。



## 基本用法

`async`函数返回一个Promise对象，可以使用`then`方法添加会带哦函数。当函数执行的时候，一旦遇到`await`就会返回，等到异步操作完成，再接着执行函数体内后面的语句。

下面是一个例子：

```javascript
async function getStockPriceName (name) {
  var symbol = await getStockSymbol(name);
  var stock = await getStockPrice(symbol);
  return stockPrice;
}

getStockPriceByName('goog').then(result => console.log(result));
```

由于`async`函数返回的是Promise对象，可以作为`await`命令的参数。所以，上面的例子也可以写成下面的形式。

```Javascript
async function timeout (ms) {
  await new Promise(resolve => {
    setTimeout(resolve, ms);
  })
};

async function asyncPrint (value, ms) {
  await timeout(ms);
  console.log(value)
};
asyncPrint('hello world', 50);
```



## 返回Promise对象

`async`函数返回一个Promise对象。

`async`函数内部`return`语句返回的值，会成为`then`方法回调函数的参数。

```Javascript
async function f() {
  return 'hello world';
}
f().then(v => console.log(v));
// hello world
```

`async`函数内部抛出错误，会导致返回的 Promise 对象变为`reject`状态。抛出的错误对象会被`catch`方法回调函数接收到。

```Javascript
async function f() {
  throw new Error('出错了');
}

f().then(
  v => console.log(v),
  e => console.log(e)
)
// Error: 出错了
```



## Promise对象的状态变化

`async`函数返回的Promise对象，必须等到内部所有`await`命令后面的Priomise对象执行完，才会发生状态改变，除非遇到`return`语句或者抛出错误。也就是说，只有`async`函数内部的异步操作执行完，才会执行`then`方法指定的回调函数。



## await命令

正常情况下，`await`命令后面是一个Promise对象。如果不是，会被转成一个立即resolve的Promise对象。



```Javascript
async function f() {
  return await 123;
}

f().then(v => console.log(v))
// 123
```

上面代码中，`await`命令的参数是数值`123`，它被转成 Promise 对象，并立即`resolve`。



有时，我们希望即使前一个异步操作失败，也不要中断后面的异步操作。这时可以将第一个`await`放在`try...catch`结构里面，这样不管这个异步操作是否成功，第二个`await`都会执行。

```javascript
async function f() {
  try {
    await Promise.reject('出错了')
  } catch (e) {
    return await Promise.resolve('hello world');
  }
}

f().then(v => console.log(v));
// hello world
```



另一种方法是`await`后面的Promise对象再跟一个`catch`方法，处理前面可能出现的错误。

```Javascript
async function f() {
  await Promise.reject('出错了').catch(e => console.log(e));
  return await Promise.resolve('hello world');
}
f().then(v => console.log(v));
// 出错了
// hello world
```



## 错误处理

如果`await`后面的异步操作出错，那么等同于`async`函数返回的Promise对象被`reject`。



```javascript
async function f() {
  await new Promise(function (resolve, reject) {
    throw new Error('出错了')；
  })；
}

f().then(v => console.log(v)).catch(e => console.lg(e));
// Error: 出错了
```



## 异步操作并行

```Javascript
// 写法一
let [foo, bar]= await Promise.all([getFoo(), getBar()]);
//写法二
let fooPromise = getFoo();
let barPromise = getBar();
let foo = await fooPromise;
let bar = await barPromise;
```

上面两种写法，`getFoo`和`getBar`都是同时触发，这样就会缩短程序的执行时间。

第三点，`await`命令只能用在`async`函数之中，如果用在普通函数，就会报错。



如果确实希望多个请求并发执行，可以使用`Promise.all`方法。当三个请求都会`resolved`时，下面两种写法效果相同。



```Javascript
async function dbFunc(db) {
  let docs = [{}, {}, {}];
  let promises = doc.map(doc => db.post(doc));
  
  let results = await Promise.all(promises);
  console.log(results);
}
// 或者使用下面的写法
async function dbFunc(db) {
  let docs = [{}, {}, {}];
  let promises = doc.map(doc => db.post(doc));
  let results = [];
  for (let promise of promises) {
    results.push(await promise);
  }
  console.log(results);
}
```



避免继发执行，使用`for`循环。
## Promise之数遍理解

说来惭愧，看了几遍的`promise`还是得去再看一遍阮老师的**ECMAScript 6入门**。

进入正题.......

还是得引申下R老师书上的金玉良言，挑几个印象深的吧。

> 所谓promise，简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。

听着觉得有点绕口，简洁明了点，promise就好比一个流程图的判断。



![图一](https://ws1.sinaimg.cn/large/006tKfTcgy1fhgdxdzrahj30n20j0ta7.jpg)

> Promise对象的特点：
>
> 对象状态不受外界影响。三种状态：`Pending`、`Resolved`、`Rejected`。
>
> 只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作无法改变这个状态。

只有异步操作的结果，可以决定当前是哪一种状态。`new Promise((resolve, reject) => cb)`这里的`cb`里如果是同步，我觉得也没必要关心状态了，异步操作因为不像同步那样结果来的及时，所以需要用一个`resolve`或者`reject`来是否已经执行完或者要把结果传给下一个回调（then操作）。从我理解和实践的经验看来，当前是什么状态，还是跟`resolve`和`reject`密切相关，跟异步操作的结果并无太大关联，还是基于场景下才能解释。



> Promise.resolve(xx) 和 Promise.reject(xx)会生成一个新的实例。如果xx是一个promise，那么会原封不动地返回这个promise。

```Javascript
Promise.resolve('foo');
// 等价于
new Promise(resolve => resolve('foo'));

Promise.reject('error');
// 等价于
new Promise((resolve, reject) => reject('error'));
```
## *fetch介绍*

*`fetch`允许你像`XMLHttpRequest`一样进行网络请求。*

*主要的区别：*

- *fetch使用promise，使用一个简单简洁的API，避免了回调和记住复杂的XMLHttpRequest API*

### *XMLHttpRequest*

*一个`XMLHttpRequst`需要2个监听分别处理成功和失败情况以及一个`open`方法和`send`方法*

```Javascript
function reqListenr () {
  var data = JSON.parse(this.responseText);
  console.log(data);
}

function reqError (err) {
  console.log('Fetch Error: -S', err);
}

var oReq = new XMLHttpRequest();
oReq.onload = reqListenr;
oReq.onerror = reqError;
oReq.open('get', './api/some.json', true); //第三个参数默认true，开启异步
oReq.send();
```



### *Fetch*

```Javascript
fetch('./api/some.json')
.then(() => {
  if (response.status !== 200) {
    console.log('Looks like there was a problem.Status Code:' + response.status);
    return;
  }
  
  //Examine the text in the response
  response.json().then((data) => { console.log(data); })
}).catch((err) => { console.log('Fetch Error : -S', err)})
```

*我们首先要通过确定`response status`是200才能把`response`解析成`JSON`。*

*`fetch()`的`response`请求是一个[Stream](https://streams.spec.whatwg.org/) 对象， 意味着当你调用`json()`方法时，一个Promise返回从你异步地读取`stream`。*



### *Response Metadata*

*当我们发起一个`fetch`请求时，响应会给出一个`response.type`，分别会有`basic`，`cors`或者`opaque`。这些类型表示资源来自哪里并且可以被用作告诉你怎么处理这个响应。*

*`basic`表示资源是同源的并且你查看响应没有任何限制。*

*`cors`表示资源是非同源的，会返回 [the CORs headers](http://enable-cors.org/)*

*`cors`和`basic`响应几乎是典型的除了`cors`响应限制了你能查看头部的一些字段，比如`Cache-Control`，`Content-Language`，`Content-Type`，`Expires`，`Last-Modified`和`Pragma`。*

*一个`opaque`响应对于一个请求的发起来自一个不同的源并且不返回`CORS headers`。对于响应是`opaque`类型的，我们无法返回的数据并且查看请求的状态*

*`status`，以为这我们无法确认请求是否成功。目前由于`fetch`实现， 请求非同源路径的资源在一个全局作用域下是不可能的。*

*你能对`fetch`的请求定义一个模式，在这个模式下只有特定的请求才会被处理。模式如下*

- `same-origin`只有对于请求在同源情况下成功，其他请求会被拒绝。
- `cors`允许请求在同源以及其他返回合适的`CORS`头部的请求。
- `cors-with-forced-preflight`总是会先执行一个预检在做出真正的请求之前。
- `no-cors`意图请求其他源上没有`CORS`头部并且导致一个`opaque`响应，正如前面所说，对于在window全局作用域下任何时候都不可能。

`mode`作为一个options对象在`fetch`请求的第二个参数，并且定义在这个options对象中：

```Javascript
fetch('http://some-site.com/cors-enabled/some.json', { mode: 'cors' })
.then((response) => response.text())
.then((text) => console.log('Request successful', text))
.catch((error) => console.log('Request failed', error))
```



### 链式Promise

Promise最大的一个特点在于能够链式调用。对于`Fetch`，它允许你跨请求分享逻辑。

如果你用到`JSON API`，你将需要确认status并且对每个响应做一下JSON的解析。

你可以在不同的返回promises的函数中定义status和解析JSON，解放你对于最终数据以及错误的处理。



```Javascript
function status (response) {
  if (response.status >= 200 && response.status < 300) {
    return Promise.resolve(response)
  } else {
    return Promise.reject(new Error(response.statusText))
  }
}

function json (response) {
  return response.json()
}

fetch('users.json')
.then(status)
.then(json)
.then((data) => console.log('Request succeeded with JSON response'), data)
.catch((error) => console.log('Request failed', error))
```

我们定义`status`函数通过确认 **response.status**以及返回`Promise.resolve()`或者`Promise.reject`是结果，返回一个被确定或者被拒绝的Promise。这是`fetch`链的第一个方法，如果它被`resolve`，我们调用`json()`方法再返回一个Promise回调。在这之后我们有一个被解析成JSON的对象、如果解析失败Promise会被拒绝并且捕获语句会执行。



### POST Request

对于web应用来讲调用一个POST方法并且在请求体中支持一些参数是不寻常。

```javascript
fetch(url, {
  method: 'post',
  headers: {
    'Content-type': 'application/x-www-form-urlencoded;charset=UTF-8'
  },
  body: 'foo=bar&lorem=ipsum'
}).then(json)
.then(data() => console.log('Request succeeded with JSON response'), data)
.catch((error) => console.log('Request failed', error))
```



如果你需要在`fetch`请求中添加`credentials`比如需要cookies，你应该设置`credentials`为 "include"。

```
fetch(url, {
  credentials: 'include'
})
```



[Introduction to fetch()](https://developers.google.com/web/updates/2015/03/introduction-to-fetch#why_is_no-cors_supported_in_service_workers_but_not_the_window)
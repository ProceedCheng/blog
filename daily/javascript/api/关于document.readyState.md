## document.readyState

### 概述

> 当[`document`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document)文档正在加载时， 返回"loading"。当文档结束渲染但在加载内嵌资源时，返回"interactive"，并引发`DOMContentLoaded`。当文档加载完成时，返回“complete”，并引发`load`事件。



readystatechange事件会在[`document`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document)对象上的readyState属性的属性值发生变化时触发。

### 纯js监听document是否加载完成

跨游览器且纯javascript检测document是否加载完成。

跨浏览器且纯javascript检测document是否加载完成的方法是使用`readyState`。

```javascript
if(document.readyState === 'complete') {
  //页面已经完全加载
}
```

这样可以在document完全加载时检测到...

```Javascript
let stateCheck = setInterval(() => {
  if (document.readyState === 'complete') {
    clearInterval(stateCheck)
    
    //document ready
  }
}, 100)
```

或者使用`onreadystatechange`

```Javascript
document.onreadystatechange = () => {
  if(document.readyState === 'complete') {
    //document ready
  }
}
```
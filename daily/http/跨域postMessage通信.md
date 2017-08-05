# 跨域postMessage通信

## postMessage()

`postMessage`方法接受两个参数：

- `message` - 一个字符串或对象能够被是发送到可接受的window
- `targetOrigin` - 被发送消息到指定window的URL。如果没有指定具体URL可以用`*`指定，表示匹配任何URL。

这个方法应该在被发送消息的window上调用。目标window的引用可以通过好几个不同的方法来获得：

- 当使用`window.open()`一个引用指向新的window能够通过`open()`方法返回
- 对于`iframes`你可以访问它的`contentWindow`属性



```Javascript
targetWindow.postMessage('Hello World', 'http://example.com')
```



## 建立Event Listeners来接受Messages

当调用一个`postMessage()`，一个`MessageEvent`能够被成功触发在可接受的`window`上。你可以利用一个标准的事件监听器去监听这个事件并且执行一些代码当事件发生时。

传入监听器回调的事件有一个属性叫做`data`，能够被用来访问通过`postMessage()`方法发送的字符串或对象。

```Javascript
window.addEventListener('message', function (e) {
  var message = e.data;
});
```



例子：

### The Controller Window

```Javascript
window.onload = function () {
  //get the window displayed in the iframe
  var receiver = document.getElementById('receiver').contentWindow;
  // get a reference to the 'Send Message' button
  var btn = document.getElementById('send');
  // A function to handle sending messages
  function sendMesage (e) {
    e.preventDefault();
    // send a message with the text 'Hello Treehouse' to receiver window
    receiver.postMessage('Hello treeHouse', 'http://demos.matt-west.com');
  }
  
 // Add an event listener that will execute the sendMesage() function when the send button is clicked
  btn.addEventListener('click', sendMessage);
}
```

### The Receiver Window

```Javascript
window.onload = function() {
  // Get a reference to the div on the page that will display the
  // message text.
  var messageEle = document.getElementById('message');

  // A function to process messages received by the window.
  function receiveMessage(e) {
    // Check to make sure that this message came from the correct domain.
    if (e.origin !== "http://s.codepen.io")
      return;

    // Update the div element to display the message.
    messageEle.innerHTML = "Message Received: " + e.data;
  }

  // Setup an event listener that calls receiveMessage() when the window
  // receives a new MessageEvent.
  window.addEventListener('message', receiveMessage);
}
```

http://blog.teamtreehouse.com/cross-domain-messaging-with-postmessage
## HTTP方法：GET对比POST

### 什么是HTTP？

超文本传输协议（HTTP）的设计目的是保证客户机与服务器之间的通信。

HTTP的工作方式是客户机与服务器之间的请求-应答协议。

web 浏览器可能是客户端，而计算机上的网络应用程序也可能作为服务器端。

举例：客户端（浏览器）向服务器提交 HTTP 请求；服务器向客户端返回响应。响应包含关于请求的状态信息以及可能被请求的内容。

### 两种HTTP请求方法：GET和POST

在客户机和服务器之间进行请求-响应时，两种最常被用到的方法是：GET和POST。

- GET - 从指定的资源请求数据
- POST - 向指定的资源提交要被处理的数据



### GET方法

**请注意，查询字符串（键值对）是在GET请求的URL中发送的：**

```http
/test/demo_form.asp?name1=value1&name2=value2
```

- GET请求可被缓存
- GET请求保留在浏览器历史记录中
- GET请求可被收藏为书签
- GET请求有长度限制
- GET请求只应当用于取回数据

### POST方法

**请注意，查询字符串（名称/值对）是在 POST 请求的 HTTP 消息主体中发送的**:

```http
POST /test/demo_form.asp HTTP/1.1
Host: w3schools.com
name1=value1&name2=value2
```

- POST请求不会被缓存
- POST不会保留在浏览器历史记录中
- POST不能被收藏为书签
- POST请求对数据长度没有要求



### 比较GET和POST

|          |                   GET                    | POST                                     |
| :------- | :--------------------------------------: | :--------------------------------------- |
| 后退按钮/刷新  |                    无害                    | 数据会被重新提交（浏览器应该告知用户数据会被重新提交）              |
| 书签       |                  可收藏为书签                  | 不可收藏为书签                                  |
| 缓存       |                   能被缓存                   | 不能缓存                                     |
| 编码类型     |    application/x-www-form-urlencoded     | application/x-www-form-urlencoded或multipart/form-data。为二进制数据使用多重编码 |
| 历史       |               参数保留在浏览器历史中                | 参数不会保存在浏览器历史中                            |
| 对数据长度的限制 | 是的。发送数据时，GET方法向URL添加数据；URL的长度是受限制的（URL的最大长度是2048个字符） | 无限制                                      |
| 对数据类型的限制 |                只允许ASCII字符                | 没有限制。也允许二进制数据。                           |
| 安全性      | 与POST相比，GET的安全性较差，因为所发送的数据时URL的一部分。在发送密码或其他敏感信息时绝不要使用GET! | POST比GET更安全，因为参数不会被保存在浏览器历史或web服务器日志中    |
| 可见性      |             数据在URL中对所有人都是可见的             | 数据不会显示在URL中                              |



### 其他HTTP请求方法

| 方法      | 描述                        |
| ------- | ------------------------- |
| HEAD    | 与GET相同，但只返回HTTP报头，不返回文档主体 |
| PUT     | 上传指定的URL表示                |
| DELETE  | 删除指定资源                    |
| OPTIONS | 返回服务器支持的HTTP方法            |
| CONNECT | 把请求连接转换到透明的TCP/IP通道       |



有的观点认为，应该用POST来创建一个资源，用PUT来更新一个资源；有的观点认为，应该用PUT来创建一个资源，用POST来更新一个资源；还有的观点认为可以用PUT和POST中任何一个来做创建或者更新一个资源。这些观点都只看到了风格，争论起来也只是争论哪种风格更好，其实，用PUT还是POST，不是看这是创建还是更新资源的动作，这不是风格的问题，而是语义的问题。



在HTTP中，PUT被定义为idempotent的方法，POST则不是，这是一个很重要的区别。



   “Methods can also have the property of "idempotence" in that (aside from error or expiration issues) the side-effects of N > 0 identical requests is the same as for a single request.”



上面的话就是说，如果一个方法重复执行多次，产生的效果是一样的，那就是idempotent的。



​      举一个简单的例子，假如有一个博客系统提供一个Web API，模式是这样http://superblogging/blogs/post/{blog-name}，很简单，将{blog-name}替换为我们的blog名字，往这个URI发送一个HTTP PUT或者POST请求，HTTP的body部分就是博文，这是一个很简单的REST API例子。我们应该用PUT方法还是POST方法？取决于这个REST服务的行为是否是idempotent的，假如我们发送两个http://superblogging/blogs/post/Sample请求，服务器端是什么样的行为？如果产生了两个博客帖子，那就说明这个服务不是idempotent的，因为多次使用产生了副作用了嘛；如果后一个请求把第一个请求覆盖掉了，那这个服务就是idempotent的。前一种情况，应该使用POST方法，后一种情况，应该使用PUT方法。



​      也许你会觉得这个两个方法的差别没什么大不了的，用错了也不会有什么问题，但是你的服务一放到internet上，如果不遵从HTTP协议的规范，就可能给自己带来麻烦。比如，没准Google Crawler也会访问你的服务，如果让一个不是indempotent的服务可以用indempotent的方法访问，那么你服务器的状态可能就会被Crawler修改，这是不应该发生的。国外文章摘录，具体忘记名称作者和url了~

更多参考：[99% 的人都理解错了 HTTP 中 GET 与 POST 的区别](https://juejin.im/entry/57597bd45bbb500053c88b4c)
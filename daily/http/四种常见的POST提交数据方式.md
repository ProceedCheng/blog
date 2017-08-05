## 四种常见的POST提交数据方式

HTTP/1.1协议规定的HTTP请求方法：

- OPTIONS
- GET
- HEAD
- POST
- PUT
- DELETE
- TRACE
- CONNECT



> HTTP协议是以ASCII码传输，建立TCP/IP协议之上的应用层规范。规范把HTTP请求分为三个部分：状态行、请求头、消息主体。类似下面：

```http
<method> <request-URL> <version>
<headers>

<entity-body>
```

协议规定POST提交的数据必须放在消息主体（entity-body）中，但协议并没有规定数据必须使用什么编码方式。实际上，开发者完全可以自己决定消息主体的格式，只要最后发送的HTTP请求满足上面的格式就可以。

但是，数据发送出去，还要服务端解析成功才有意义。一般服务端语言php、pthyon等，以及它们的Framework，都内置了自动解析常见数据格式的功能。服务端通常是根据请求头（headers）中的Content-type字段来获知请求中的消息主体是用何种方式编码，再对主体进行解析。所以说到POST提交数据方案，包含了Content-type和消息主体编码方式两部分。



[参考链接](https://imququ.com/post/four-ways-to-post-data-in-http.html)
## css collapsing margin解决办法

解决办法有如下：

- **浮动元素、inline-block 元素、绝对定位元素的 margin 不会和垂直方向上其他元素的 margin 折叠 ( 针对 兄弟元素)**

  **注意: 浮动元素 , inline-block元素 , 绝对定位元素 都属于 BFC元素。**

- **创建了块级格式化上下文(BFC, blocking formatting context )的父元素，比如说overflow:hidden，不和它的子元素发生 margin 折叠 (针对 父子元素)。**

- **给父元素添加以下内容之一都可以避免发生 margin 重叠 。如 添加内容 ， 添加 padding ， 添加 border。**



虽然有方法解决这个问题。但是目前最好的解决方案是回避这个问题。也就是，不要给**指定元素添加具有指定宽度的内边距或外边距**，而是尝试将内边距或外边距添加到元素的父元素和子元素。



**注：margin-left margin-top 是自身的偏移，margin-right margin-bottom是对周围文档流中的元素的偏移**

转自[链接](http://www.cnblogs.com/Uncle-Keith/p/5786458.html)

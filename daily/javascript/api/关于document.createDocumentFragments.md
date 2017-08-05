## 关于document.createDocumentFragment

| 方法                       | 说明               |
| ------------------------ | ---------------- |
| createAttribute(name)    | 用指定名称name创建特性节点  |
| createComment(text)      | 创建带文本texty的注释节点  |
| createDocumentFragment() | 创建文档碎片节点         |
| createElement(tagname)   | 创建标签名为tagname的节点 |
| createTextNode(text)     | 创建包含文本text的文本节点  |



以上这些方法，每次Javascript对DOM的操作都会改变当前页面的呈现，并重新刷新整个页面，从而消耗了大量的时间。为解决这个问题，**可以创建一个文档碎片，把所有新节点附加其上，然后把文档碎片的内容一次性添加到document中，这个就是createDocumentFragment()的用武之处。**



**DocumentcFragment**: 表示文档的一部分更确切地说，它表示一个或多个邻接的 Document 节点和它们的所有子孙节点。

**DocumentFragment节点不属于文档树，继承parentNode属性总是null**

DocumentFragment有一种特殊的行为，当请求把一个DocumentFragment节点插入文档树时，插入的不是DocumentFrament本身，而是它的所有子孙节点。这使得DocumentFragment成了有用的占位符，暂时存放那些**一次插入**的文档节点。有利于实现文档的剪切和赋值黏贴操作。



说明：添加多个dom元素时，先将元素append到DocumentFragment中，最后统一将DocumentFragment添加到页面。**该做法可以减少页面渲染dom元素的次数。**

### createDocumentFragment()用法示例

```javascript
var frag = document.createDocumentFragment();
for(var i = 0; i < 1000; i++) {
  var el = document.createElement('p');
  el.innerHTML = i;
  frag.appendChild(el);
}

document.body.appendChild(frag);
```



关于createElement和createDocumentFragment之间的区别：

1. createElement创建的元素可以使用 **innerHTML**，createDocumentFragment创建的元素使用innerHTML无法达到预期的修改文档内容的效果，只是作为一个属性而已。两者的节点类型完全不同，**并且createDocumentFragment创建的元素在文档中没有对应的标记**，因此在页面上只能用js中访问到。
2. createElement创建的元素可以重复操作，添加后可以从文档中移除，createDocumentFragment创建的元素是一次性的，添加之后不能操作了。（**说明：这里的添加并不是添加了新创建的片段，因为上面说过，新创建的片段在文档内是没有对应的标签的，这里添加的是片段的所有子节点**）
## 关于js取不到元素的style.left属性

当你要取一个element.style.left的时候，只有在这个元素有内联style的情况下才能取到。如果这个left属性是写在样式文件里，你不能按照你的期望取到这个值。



### window.getComputedStyle

取到元素计算好的style通过使用`window.getComputedStyle`，问题是只适用于IE9+以上的浏览器。可以包装一个方法：

```Javascript
function getCssProperty(elmId, property) {
  var elem = document.getElementById(elmId);
  return window.getComputedStyle(elem, null).getPropertyValue(property);
}

var left = getCssProperty('my-div', 'left');
```



### jQuery (or some other library)

另外一个选择是使用jquery的`.css()`方法。



## 备注

getComputedStyle 的返回值是 [`resolved values`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/resolved_value),  通常跟CSS2.1中的[`computed values`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/computed_value)是相同的值。 但对于一些旧的属性，比如width, height, padding 它们的值又为 [`used values`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/used_value)。 最初, CSS2.0定义的计算值Computed values 就是属性的最终值。 但是CSS2.1 重新定义了 computed values 为布局前的值， used values布局后的值。 布局前与布局后的区别是， width 或者 height的 百分比可以代表元素的宽度，在布局后，会被像素值替换.



[[How to get the CSS left-property value of a div using JavaScript?](https://stackoverflow.com/questions/13778439/how-to-get-the-css-left-property-value-of-a-div-using-javascript)](https://stackoverflow.com/questions/13778439/how-to-get-the-css-left-property-value-of-a-div-using-javascript)


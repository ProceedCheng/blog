## rem适配

```javascript
!function (doc, win) {
  var docEle = doc.documentElement,
      evt = "onorientationchange" in window ? "orientationchange" : "resize",
      fn = function () {
        var width = docEle.clientWidth;
        width && (docEle.style.fontSize = width / 6.4 + "px");
        // 等价于html.style.fontSize = windowWidth / 640 * 100 + 'px';
        // 这个6.4就是根据设计稿的横向宽度来确定的，假如你的设计稿是750
// 那么 html.style.fontSize = windowWidth / 7.5 + 'px';
      }
  
  win.addEventListener(evt, fn , false);
  doc.addEventListener("DOMContentLoaded", fn , false)
  
}(document, window)
```



### Css3 media query

```Css
@media screen and (min-width: 320px) {
    html {font-size: 14px;}
}
 
@media screen and (min-width: 360px) {
    html {font-size: 16px;}
}
 
@media screen and (min-width: 400px) {
    html {font-size: 18px;}
}
 
@media screen and (min-width: 440px) {
    html {font-size: 20px;}
}
 
@media screen and (min-width: 480px) {
    html {font-size: 22px;}
}
 
@media screen and (min-width: 640px) {
    html {font-size: 28px;}
}
```





```javascript
var dpr, rem, scale;
var docEl = document.documentElement;
var fontEl = document.createElement('style');
var metaEl = document.querySelector('meta[name="viewport"]');

dpr = window.devicePixelRatio || 1;
rem = docEl.clientWidth * dpr / 10;
scale = 1 / dpr;


// 设置viewport，进行缩放，达到高清效果
metaEl.setAttribute('content', 'width=' + dpr * docEl.clientWidth + ',initial-scale=' + scale + ',maximum-scale=' + scale + ', minimum-scale=' + scale + ',user-scalable=no');

// 设置data-dpr属性，留作的css hack之用
docEl.setAttribute('data-dpr', dpr);

// 动态写入样式
docEl.firstElementChild.appendChild(fontEl);
fontEl.innerHTML = 'html{font-size:' + rem + 'px!important;}';

// 给js调用的，某一dpr下rem和px之间的转换函数
window.rem2px = function(v) {
v = parseFloat(v);
return v * rem;
};
window.px2rem = function(v) {
    v = parseFloat(v);
    return v / rem;
};

window.dpr = dpr;
window.rem = rem;
```

[动态计算rem](http://xuebin.me/2017/01/07/%E5%8A%A8%E6%80%81%E8%AE%A1%E7%AE%97rem/)

[rem 产生的小数像素问题](http://taobaofed.org/blog/2015/11/04/mobile-rem-problem/)

[[关于移动端 rem 布局的一些总结](https://segmentfault.com/a/1190000003690140)](https://segmentfault.com/a/1190000003690140)

[移动端高清、多屏适配方案](http://div.io/topic/1092)
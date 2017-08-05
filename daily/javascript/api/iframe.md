# iframe

## 普通方法加载iframe

使用这种加载方法会在浏览器中有如下表现：

- iframe会在主页面onload之前加载
- iframe会在所有iframe内容都加载完毕之后触发iframe的onload
- 主页面的onload会在iframes的onload触发之后触发，所以iframe会阻塞页面的加载
- 当iframe在加载的过程中，浏览器会标识正在加载东西，处于忙碌状态。
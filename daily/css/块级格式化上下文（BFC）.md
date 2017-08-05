## 块级格式化上下文（BFC）

### 特性

1. 内部的Box会在垂直方向，从顶部开始一个接一个地放置。
2. Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生叠加。
3. 每个元素的margin box的左边，与包含border box的左边相接触（对于从左往右的格式化，否则相反）。即使浮动元素也是如此。
4. BFC的区域不会与float box叠加。
5. BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响外面的元素，反之亦然。
6. 计算BFC的高度，浮动元素也会参与计算。

### 如何触发BFC

1. float除了none以外的值
2. overflow除了visible以外的值(hidden, auto, scroll)
3. display （table-cell, table-caption, inline-block, flex, inline-flex）
4. position值为(absolute, fixed)
5. fieldset元素



以上条件会触发BFC。

> 解决margin叠加问题可以在元素外套一个div 设置 overflow:hidden。

清浮动防止高度塌陷也是由于BFC的存在。
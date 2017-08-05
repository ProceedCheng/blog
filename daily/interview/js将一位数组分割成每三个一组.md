## [js将一位数组分割成每三个一组](https://segmentfault.com/q/1010000004921251)

解法一：

```Javascript
var data = ['法国','澳大利亚','智利','新西兰','西班牙','加拿大','阿根廷','美国','0','国产','波多黎各','英国','比利时','德国','意大利','意大利',];
var result = [];
for(var i=0,len=data.length;i<len;i+=3){
   result.push(data.slice(i,i+3));
}
```



解法二（灵活）：

```javascript
var arr = ['法国','澳大利亚','智利','新西兰','西班牙','加拿大','阿根廷','美国','0','国产','波多黎各','英国','比利时','德国','意大利','意大利'],
    newArr = [],
    b;
arr.forEach(function(item, index, array) {
    var a = Math.floor(index / 3);
    if (b !== a) {
        b = a;
        newArr[a] = new Array();
    }
    newArr[a].push(item);         
});
```


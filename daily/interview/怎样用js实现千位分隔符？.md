## 怎样用js实现千位分隔符？

1.不考虑小数（个人实现）

```javascript
function splitOfThousand(num) {
  
  if(num.length <= 3) {
    return num;
  }
  var numArr = num.split('');
  
   
  function splitNum(numArr) {
    var newArr = [];
    if(numArr.length > 3) {
      var firstArr = numArr.slice(0, numArr.length - 3);
      var lastArr = numArr.slice(numArr.length - 3);
      lastArr.unshift(",");
      newArr = (firstArr.length > 3) ? newArr.concat(splitNum(firstArr), lastArr) : newArr.concat(firstArr, lastArr)
    }
    
    return newArr;
  }
  
  return splitNum(numArr).join('')
}


var result = splitOfThousand("1236662323323223");

console.log(result); //"1,236,662,323,323,223"

```





解法一：



```javascript
function numFormat(num) {
  num = num.toString().split('.');
  var arr = num[0].split('').reverse();
  var res = [];
  for(var i = 0, len = arr.length; i < len; i++) {
    if(i !== 0 && i % 3 === 0) {
      res.push(',');
    }
    res.push(arr[i])
  }
  res.reverse();
  if(num[1]) {
    res.join('').concat('.' + num[1]);
  } else {
    res = res.join('')
  }
  
  return res;
}

var a=1234567894532;
var b=673439.4542;
console.log(numFormat(a)); // "1,234,567,894,532"
console.log(numFormat(b)); // "673,439.4542"

```



解法二：

> 使用js自带的toLocaleString
>
> 语法： `numObj.toLocaleString([locales [, options]])`

`toLocaleString()` 方法返回这个数字在特定语言环境下的表示字符串。



```javascript
var a=1234567894532;
var b=673439.4542;

console.log(a.toLocaleString());  // "1,234,567,894,532"
console.log(b.toLocaleString());  // "673,439.454"  （小数部分四舍五入了）
```



解法三：

使用正则表达式和`replace`函数

> replace 语法：`str.replace(regexp|substr, newSubStr|function)`

其中第一个 [RegExp ](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/RegExp)对象或者其字面量所匹配的内容会被第二个参数的返回值替换。

```Javascript
function numFormat(num) {
  var res = num.toString().replace(/\d+/, function(n) {
    return n.replace(/(\d)(?=(\d{3})+$)/g, function($1) {
      return $1 + ","
    });
  })
  
  return res;
}

var a=1234567894532;
var b=673439.4542;
console.log(numFormat(a)); // "1,234,567,894,532"
console.log(numFormat(b)); // "673,439.4542"

```





[链接](http://www.jianshu.com/p/928c68f92c0c)
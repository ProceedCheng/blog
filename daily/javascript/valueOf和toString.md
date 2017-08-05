### valueOf和toString

1.当需要将对象转成字符串时，如果遇到+和==以及!=操作，优先采用valueOf，否则采用toString，如下：



```javascript
var Parent = function(){};
Parent.prototype.toString = function(){
    return 'parent';
};
var Child = function(){};
Child.prototype = new Parent();
 
var obj = new Child();
alert(obj);  //弹出：'parent'

```

2.当进行==比较时，优先采用valueOf方法（优先级高于toString），这个过程同样会一直往原型链上追溯，如下：

```javascript
var Parent = function(){};
Parent.prototype.valueOf = function(){
    return 'Parent valueOf';
};
var Child = function(){};
Child.prototype = new Parent();
 
var obj = new Child();
alert(obj=='Parent valueOf');  //弹出 true
```



**但Date是个特例，在==比较时，Date会优先采用toString转换，至于为什么，应该是历史原因吧（求科普）**



3.当需要将对象转成数字时，优先采用valueOf，否则采用toString（没有valueOf或valueOf不适合，但没有valueOf的场景是？？），用例略
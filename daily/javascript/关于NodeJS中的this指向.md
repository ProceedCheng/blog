## 关于NodeJS中的this指向

在浏览器中this指的是全局对象 **window**，而在Node.js中this指向的是模块(module)作用域。



```javascript
function fn(){
    this.num = 10;
}
fn();
console.log(this); {}
console.log(this.num); undefined
console.log(global.num); 10
```

可以得出在nodejs中函数体内this定义的属性默认指向的是global，而在函数外输出this却不是global而是一个空对象，这也正是之前所说的模块作用域。
# es6扩展运算符

```Javascript
console.log(...[1, 2, 3])
// 1 2 3

console.log(1, ...[2, 3, 4], 5)
// 1 2 3 4 5

[...document.querySelectorAll('div')]
// [<div>, <div>, <div>]

function push(array, ...items) {
  array.push(...items);
}

function add(x, y) {
  return x + y;
}

var numbers = [4, 38];
add(...numbers) // 42
```

上面代码中，`array.push(...items)`和`add(...numbers)`这两行，都是函数的调用，它们都使用了扩展运算符。**该运算符将一个数组，变为参数序列。**

## 替代apply方法



## 扩展运算符的应用

### 合并数组

```Javascript
// es5
[1, 2].concat(more)
// es6
[1, 2, ...more]

var arr1 = ['a', 'b']
var arr2 = ['c']
var arr3 = ['d', 'e']

// es5的合并数组
arr1.concat(arr2, arr3)
// ['a', 'b', 'c', 'd', 'e']

// es6的合并数组
[...arr1, ...arr2, ...arr3]
```

### 与解构赋值结合

扩展运算符可以与结构赋值结合起来，用于生成数组。

```Javascript
// es5
a = list[0], rest = list.slice(1)
// es6
[a, ...rest] = list

const [first, ...rest] = [1, 2, 3, 4, 5, 6]
first // 1
rest // [2, 3, 4, 5]

const [first, ...rest] = []
first // undefined
rest // []

const [first, ...rest] = ['foo']
first // 'foo'
rest // []
```

如果将扩展运算符用于数组赋值，**只能放在参数的最后一位**，否则会报错

```Javascript
const [...butlast, last] = [1, 2, 3, 4, 5];
// 报错
const [first, ...middle, last] = [1, 2, 3, 4, 5];
// 报错
```



### 函数的返回值

Javascript的函数只能返回一个值，如果需要返回多个值，只能返回数组或对象。扩展运算符提供了解决这个问题的一种变通方法。

```Javascript
var dateFields = readDateFields(database);
var d = new Date(...dateFields);
```

上面的代码从数据库取出一行数据，通过扩展运算符，直接将其传入构造函数`Date`。

### 字符串

扩展运算符还可以将字符串转为真正的数组。

```Javascript
[...'hello'] // ['h', 'e', 'l', 'l', 'o']
```

上面的写法，有一个重要的好处，那就是能够正确识别32位的Unicode字符。

```Javascript
'x\uD83D\uDE80y'.length // 4
[...'x\uD83D\uDE80y'].length // 3
```

上面的代码的第一种写法，Javascript会将32位Unicode字符，识别为2个字符，采用扩展运算符就没有这个问题。因此，正确返回字符串长度的函数，可以像下面这丫鞥写。

```Javascript
function length(str) {
  return [...str].length
}

length('x\uD83D\uDE80y')
```

凡是涉及到操作32位Unicode字符的函数，都有这个问题。因此，最好都用扩展运算符改写。

```Javascript
let str = 'x\uD83D\uDE80y';

str.split('').reverse().join('')
// 'y\uDE80\uD83Dx'

[...str].reverse().join('')
// 'y\uD83D\uDE80x'
```



### 实现了Iterator接口的对象

任何Iterator接口的对象，都可以用扩展运算符转为真正的数组。

```Javascript
var nodeList = document.querySelectorAll('div')
var array = [...nodeList];
```

上面的代码中，`querySelectorAll`方法返回的是一个`nodeList`对象。它不是数组，而是一个类似数组的对象。这时，扩展运算符可以将其转为真正的数组，原因就在于`NodeList`对象实现了 Iterator 。

对于那些没有部署Iterator接口的类似数组的对象，扩展运算符就无法将其转为真正的数组。

```Javascript
let arrayLike = {
  '0': 'a',
  '1': 'b',
  '2' '从，
  length: 3
};

// TypeError: Cannot spread non-iteratable object
let arr = [...arryLike];
```

上面代码中，`arrayLike`是一个类似数组的对象，但是没有部署 Iterator 接口，扩展运算符就会报错。这时，可以改为使用`Array.from`方法将`arrayLike`转为真正的数组。



### Map和set结构，Generator函数

扩展运算符内部调用的是数据结构的Iterator接口，因此只要具有Iterator接口的对象，都可以使用扩展运算符，比如Map结构。 **能使用扩展运算符的前提是需要具备Iterator接口**

```Javascript
let map = new Map([
  [1, 'one'],
  [2, 'two'],
  [3, 'three']
]);

let arr = [...map.keys()]; //[1, 2, 3]
```

Generator 函数运行后，返回一个遍历器对象，因此也可以使用扩展运算符。

```Javascript
var go = function *() {
  yield 1;
  yield 2;
  yield 3;
}

[...go()]// [1, 2, 3]
```

上面代码中，变量`go`是一个 Generator 函数，执行后返回的是一个遍历器对象，对这个遍历器对象执行扩展运算符，就会将内部遍历得到的值，转为一个数组。

如果对没有 Iterator 接口的对象，使用扩展运算符，将会报错。

```Javascript
var obj = {a: 1, b: 2};
let arr = [...obj]; // TypeError: Cannot spread non-iterable object
```
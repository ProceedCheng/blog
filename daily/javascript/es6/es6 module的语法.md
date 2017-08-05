# es6 module的语法

> es6的模块的设计思想，是尽量的 **静态化**，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。CommonJS和AMD模块，都只能在运行时确定这些东西，比如CommonJS模块就是对象，输入时必须查找对象属性。

```Javascript
// CommonJS模块
let { stat, exists, readFile } = require('fs');

// 等同于
let _fs = require('fs');
let stat = _fs.stat;
let exists = _fs.exists;
let readfile = _fs.readfile;
```

上面代码的实质是正题加载`fs`模块（即加载`fs`的所有方法），生成一个对象(`_fs`)，然后再从这个对象上面读取3个方法。这种加载称为“运行时加载”，因为只有运行时才能得到这个对象，导致完全没办法在编译时“静态优化”。

**ES6模块不是对象，而是通过`export`命令显示指定输出的代码，再通过`import`命令输入。**

```Javascript
// es6模块
import { stat, exists, readFile } from 'fs';
```

上面代码的实质是从`fs`模块加载3个方法，其他方法不加载。这种加载称为"编译时加载"或者 **静态加载**，即ES6可以在编译时就完成模块加载，效率比CommonJS模块的加载方法高。当然，这也导致了没法引用ES6模块本身，因为它不是对象。

> 注意：es6模块中，顶层的`this`指向`undefined`，即不应该在顶层代码使用`this`。



## export

`export`语句输出的接口，与其对应的值是动态绑定关系，即通过该接口，可以取到模块内部实时的值。

```Javascript
export var foo = 'bar';
setTimeout(() => foo = 'baz', 500);
```

上面代码输出变量`foo`，值为`bar`，500ms之后变为baz。

这一点与CommonJS规范完全不同。CommonJS模块输出的是值的缓存，不存在动态更新。

`export`命令可以出现在模块的任何位置，只要处于模块顶层就可以。如果处于块级作用域内，就会报错，同`import`。因为是静态编译。

```Javascript
function foo() {
  export default 'bar' // SyntaxError
}
foo()
```



> 注意，`import`命令具有提升效果，会提升到整个模块的头部，首先执行。

```Javascript
foo();
import { foo } from 'my_module';
```

上面的代码不会报错，因为`import`的执行早于`foo`的调用。这种行为的本质是，`import`命令是编译阶段执行的，在代码执行之前。

由于`import`是静态运行，所以不能使用表达式和变量，这些只有在运行才能得到结果的语法结构。

```Javascript
// 报错
import { 'f' + 'oo' } from 'my_module';

// 报错
let module = 'my_module';
import { foo } from module;

// 报错
if (x === 1) {
  import { foo } from 'module1';
} else {
  import { foo } from 'module2';
}
```

上面三种写法都会报错，因为它们用到了表达式、变量和`if`结构。在静态分析阶段，这些语法都是没法得到值的。

最后，`import`语句会执行所加载的模块，因此可以有下面的写法。



目前阶段，通过 Babel 转码，CommonJS 模块的`require`命令和 ES6 模块的`import`命令，可以写在同一个模块里面，但是最好不要这样做。因为`import`在静态解析阶段执行，所以它是一个模块之中最早执行的。下面的代码可能不会得到预期结果。

```Javascript
require('core-js/modules/es6.symbol');
require('core-js/modules/es6.promise');
import React from 'React';
```

正是因为`export default`命令其实只是输出一个叫做`default`的变量，所以它后面不能跟变量声明语句。

```Javascript
// 正确
export var a = 1;

// 正确
var a = 1;
export default a;

// 错误
export default var a = 1;

```

上面代码中，`export default a`的含义是将变量`a`的值赋给变量`default`。所以，最后一种写法会报错。

同样地，因为`export default`本质是将该命令后面的值，赋给`default`变量以后再默认，所以直接将一个值写在`export default`之后。

```Javascript
// 正确
export default 42;

// 报错
export 42;
```

上面代码中，后一句报错是因为没有指定对外的接口，而前一句指定外对接口为`default`。
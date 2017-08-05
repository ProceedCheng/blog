### js浅复制和深复制

> 浅复制和深复制都可以实现已有对象的基础上再生一份的作用，但是对象的实例是存储在堆内存中然后通过一个引用值去操作对象，由此复制的时候就存在两种情况了：复制引用和复制实例，这也是浅复制和深复制的区别所在。

- 浅复制：浅复制是复制引用，复制后的引用都是指向同一个对象实例，彼此之间的操作会互相影响。
- 深复制：深复制不是简单的复制引用，而是在队中重新分配内存，并且把源对象实例的所有属性进行新建复制，以保证深复制的对象的引用不包含任何原有对象或对象上的任何对象，复制后的对象与原来的对象是完全隔离的。



### 利用JSON对象的parase和stringify

JSON对象的parse方法可以将JSON字符串反序列化成js对象，stringify方法可以将js对象序列化JSON字符串，借助这两个方法可以实现对象的深复制。



```javascript
    var source = {
        name:"source",
        child:{
            name:"child"
        }
    }
    var target = JSON.parse(JSON.stringify(source));
    //改变target的name属性
    target.name = "target";
    console.log(source.name);   //source
    console.log(target.name);   //target
    //改变target的child
    target.child.name = "target child";
    console.log(source.child.name);  //child
    console.log(target.child.name);  //target child
```

注意：对于正则表达式类型、函数类型、Date等无法进行深复制（而且会直接丢失相应的值），同时如果对象中存在循环引用的情况也无法正确处理。



### 实现简单深复制

```javascript
var cloneObj = function(obj) {
    var str,
        newObj = obj.constructor === Array ? [] : {};

    if(typeof obj !== 'object') {
        return;
    } else if(window.JSON) {
        str = JSON.stringify(obj);
        newObj = JSON.parse(str);
    } else {
        for(var i in obj) {
            if(obj.hasOwnProperty(i)) {
                newObj[i] = typeof obj[i] === 'object' ? cloneObj(obj[i]) : obj[i]
            }
        }
    }

    return newObj;
}

var obj = {
    arr: [1, 2, 3],
    b: {
        c: 1,
        d: 2
    }
}


var newobj = cloneObj(obj);

console.log(newobj); 
```

注意：这个函数可以深复制 对象和数组



### jQuery extend源码

```javascript
jQuery.extend = jQuery.fn.extend = function() {
	var src, copyIsArray, copy, name, options, clone,
		target = arguments[0] || {},
		i = 1,
		length = arguments.length,
		deep = false;

	// Handle a deep copy situation
	if ( typeof target === "boolean" ) {
		deep = target;

		// skip the boolean and the target
		target = arguments[ i ] || {};
		i++;
	}

	// Handle case when target is a string or something (possible in deep copy)
	if ( typeof target !== "object" && !jQuery.isFunction(target) ) {
		target = {};
	}

	// extend jQuery itself if only one argument is passed
	if ( i === length ) {
		target = this;
		i--;
	}

	for ( ; i < length; i++ ) {
		// Only deal with non-null/undefined values
		if ( (options = arguments[ i ]) != null ) {
			// Extend the base object
			for ( name in options ) {
				src = target[ name ];
				copy = options[ name ];

				// Prevent never-ending loop
				if ( target === copy ) {
					continue;
				}

				// Recurse if we're merging plain objects or arrays
				if ( deep && copy && ( jQuery.isPlainObject(copy) || (copyIsArray = jQuery.isArray(copy)) ) ) {
					if ( copyIsArray ) {
						copyIsArray = false;
						clone = src && jQuery.isArray(src) ? src : [];

					} else {
						clone = src && jQuery.isPlainObject(src) ? src : {};
					}

					// Never move original objects, clone them
					target[ name ] = jQuery.extend( deep, clone, copy );

				// Don't bring in undefined values
				} else if ( copy !== undefined ) {
					target[ name ] = copy;
				}
			}
		}
	}

	// Return the modified object
	return target;
};
```





**注意：extend只能复制可对象可枚举的属性**



## 什么是对象序列化

对象序列化是指将对象的状态转换为字符串（来自我这菜鸟的理解，好像有些书上也是这么说的，浅显易懂！）；

**序列化(Serialization)是将对象的状态信息转换为可以存储或传输的形式的过程（来自“百度百科—序列化“，学术性强，略显高端）；**

## 序列化规则

1. 对于Javascript的五种原始类型，JSON语法支持数字、字符串、布尔值、null四种，不支持undefined
2. NaN、Infinity和-Infinity序列化的结果是null
3. JSON语法不支持函数
4. 除了RegExp、Error对象，JSON语法支持其他所有对象。
5. 日期对象序列化的结果是ISO格式的字符串，但JSON.parse()依然保留它们字符串形态，并不会将它们还原为日期对象；
6. JSON.stringify()只能序列化对象的可枚举自有属性。
## 实现一个extend

```javascript
void function(global){
    var extend,
        _extend,
        _isObject;

    _isObject = function(o){
        return Object.prototype.toString.call(o) === '[object Object]';
    }

    _extend = function self(destination, source) {
        var property;
        for (property in destination) {
            if (destination.hasOwnProperty(property)) {

                // 若destination[property]和sourc[property]都是对象，则递归
                if (_isObject(destination[property]) && _isObject(source[property])) {
                    self(destination[property], source[property]);
                };

                // 若sourc[property]已存在，则跳过
                if (source.hasOwnProperty(property)) {
                    continue;
                } else {
                    source[property] = destination[property];
                }
            }
        }
    }

    extend = function(){
        var arr = arguments,
            result = {},
            i;

        if (!arr.length) return {};

        for (i = arr.length - 1; i >= 0; i--) {
            if (_isObject(arr[i])) {
                _extend(arr[i], result);
            };
        }

        arr[0] = result;
        return result;
    }

    global.extend = extend;
}(window)
```



## 2

```Javascript
function extend(){
    for(var i=1; i<arguments.length; i++)
        for(var key in arguments[i])
            if(arguments[i].hasOwnProperty(key)) { 
                if (typeof arguments[0][key] === 'object'
                    && typeof arguments[i][key] === 'object')
             				extend(arguments[0][key], arguments[i][key]);
                else
                   arguments[0][key] = arguments[i][key];
             }
    return arguments[0];
}

document.getElementsByTagName('div')[0].innerHTML = JSON.stringify(extend({
a:3,b:5, f: { r: 2, x: 8 }
}, {
a:4,c:7, f: { r: 4, s: 8}
}));
```

[转自链接](https://segmentfault.com/a/1190000000450864)
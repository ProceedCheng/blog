## 多层json对象嵌套的扁平化与去扁平化

```javascript
//扁平化
    JSON.flatten = function(data) {
        var result = {};
        function recurse(cur, prop) {
            if (Object(cur) !== cur) {
                result[prop] = cur;
            } else if (Array.isArray(cur)) {
                for (var i = 0, l = cur.length; i < l; i++)
                    recurse(cur[i], prop + "[" + i + "]");
                if (l == 0)
                    result[prop] = [];
            } else {
                var isEmpty = true;
                for (var p in cur) {
                    isEmpty = false;
                    recurse(cur[p], prop ? prop + "." + p : p);
                }
                if (isEmpty && prop)
                    result[prop] = {};
            }
        }
        recurse(data, "");
        return result;
    };
    
    //去扁平化
    JSON.unflatten = function(data) {
        "use strict";
        if (Object(data) !== data || Array.isArray(data))
            return data;
        var regex = /\.?([^.\[\]]+)|\[(\d+)\]/g,
            resultholder = {};
        for (var p in data) {
            var cur = resultholder,
                prop = "",
                m;
            while (m = regex.exec(p)) {
                cur = cur[prop] || (cur[prop] = (m[2] ? [] : {}));
                prop = m[2] || m[1];
            }
            cur[prop] = data[p];
        }
        return resultholder[""] || resultholder;
    };
```



测试：

```javascript
var obj = {
        ToUserName: ['阿斯顿'],
        FromUserName: ['啊实打实的'],
        CreateTime: ['hhhh123123123', {"x": "aaa"},[1, 2, 3, 4]],
        MsgType: ['event'],
        Event: ['sdf '],
        EventKey: [''],
        xx: 'xx'
    }

```



结果：

```json
{
    CreateTime[0]: "hhhh123123123",
    CreateTime[1].x: "aaa",
    CreateTime[2][0]: 1,
    CreateTime[2][1]: 2,
    CreateTime[2][2]: 3,
    CreateTime[2][3]: 4,
    EventKey[0]: "",
    Event[0]: "sdf ",
    FromUserName[0]: "啊实打实的",
    MsgType[0]: "event",
    ToUserName[0]: "阿斯顿",
    xx: "xx"
}

```
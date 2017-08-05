## 实现一个简单的异步方案（类promise）

```javascript
function P(fn) {
  var value = null;
  var events = [];
  this.then = function(f) {
    events.push(f);
    return this;
  }
  
  function resolve(newValue) {
    vat f = events.shift();
    f(newValue, resolve);
  }
  
  fn(resolve);
}

function a() {
  return new P(function(resolve) {
    console.log('get...');
    setTimeout(function() {
      console.log('get 1');
      resolve(1)
    }, 1000);
  });
}

a().then(function(value, resolve) {
  console.log('get ...');
  setTimeout(function() {
    console.log('get 2');
    resolve(2);
  }, 1000);
}).then(function(value, resolve) {
  console.log(value);
});

//get...
//get 1
//get...
//get 2
//2
```



## 发布/订阅

```Javascript
var Event = (function() {
  var clientList = {},
  		listen,
  		trigger,
  		remove;
  		
  listen = function(key, id, fn) {
    if(!clientList[key]) {
      clientList[key] = []
    }
    clientList[key].push({
      id: id,
      fn: fn
    });
    
    trigger = function() {
      var key = Array.prototype.shift.call(arguments),
      		fns = clientList[key];
      		
      	if(!fns || fns.length === 0) {
          return false;
      	}
      	
      	for(var i = 0; i < fns.length; i++) {
          fns[i].fn.apply(this, arguments);
      	}
    };
    
    remove = function(key, id) {
      var fns = clientList[key];
      
      if(!fns) {
        return false;
      }
      
      if(!id) {
        fns && (fns.length = 0)
      } else {
        for(var l = fns.length - 1; l >= 0; l--) {
          var _id = fns[l].id;
          if(_id === id) {
            fns.splice(l, 1);
          }
        }
      };
      
      return {
        listen: listen,
        trigger: trigger,
        remove: remove
      }
    }
  }
})();
```



### es6简单发布/订阅模式

```Javascript
'use strict';

class EmitterEvent {
    constructor() {
        this._event = {}
    }
    //订阅
    on(eventName, handler) {
        if(this._event[eventName]) {
            this._event[eventName].push(handler)
        } else {
            this._event[eventName] = [handler]
        }
    }

    emit(eventName) {
        const events = this._event[eventName];

        const otherArgs = Array.prototype.slice.call(arguments, 1);

        const that = this;

        if(events) {
            events.forEach((event) => {
                event.apply(that, otherArgs);
            })
        }
    }
    //解除订阅
    off(eventName, handler) {
        const events = this._event[eventName];

        this._event[eventName] = events.filter((event) => {
            return event !== handler;
        })
    }

    //订阅以后，发布执行一次直接解除
    once(eventName, handler) {
        const that = this;
        function func() {
            const args = Array.prototype.slice.call(arguments, 0);
            handler.apply(that, args);
            this.off(eventName, func);
        }

        this.on(eventName, func)
    }
}

const event = new EmitterEvent();

function a(something) {
    console.log(something, 'aa-aa')
}

function b(something) {
    console.log(something)
}

event.once('dosomething', a)

event.emit('dosomething', 'chifan');


```
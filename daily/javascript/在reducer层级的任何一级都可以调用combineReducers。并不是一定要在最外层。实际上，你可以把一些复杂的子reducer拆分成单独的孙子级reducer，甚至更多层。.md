在reducer层级的任何一级都可以调用combineReducers。并不是一定要在最外层。实际上，你可以把一些复杂的子reducer拆分成单独的孙子级reducer，甚至更多层。

```javascript
var combineReducer1 = function(obj) {
  //内部具体代码
  
  var finalState = {};
  function reducer(state, action) {
    //reducer具体逻辑
    for(var p in obj) {
      finalState[p] = obj[p](state[p], action);
    }
    
    //返回state
    return finalState;
  }
  
  //返回最终的reducer
  return reducer;
}
```

因为我们reducer()中方法里传入的state，其实是根state，所以根据属性名来获取相应的reducer上的state。



**combineReducers** 生成了一个类似于Reducer的函数。为什么类似于，因为它不是真正的Reducer，它只是一个调用Reducer的函数，只不过它接受的参数和真正的Reducer一模一样~

这是一部分核心代码：

```Javascript
function combineReducers(reducers) {
  //过滤reducers，把非function类型的过滤掉~
  var finalReducers = pick(reducers, (val) => typeof val === 'function');
  //一开始我一直以为这个没啥用，后来我发现，这个函数太重要了。它在一开始，就已经把你的State改变了。变成了，Reducer的key 和 Reducer返回的initState组合。
  var defaultState = mapValues(finalReducers, () => undefined);
  
  return function combination(state = defaultState, action) {
    //finalReducers 是 reducers
    var finalState = mapValues(finalReducers, (reducer, key) => {
      
      //state[key] 是当前Reducer所对应的State，可以理解为当前的State
      var previousStateForKey = state[key];
      var nextStateForKey = reducer(previousStateForKey, action);
      
      return nextStateForKey;
    });
    
    finalState 是 Reducer和key和state的组合
  }
}
```

从上面源码可以看出，combineReducers生成一个类似于Reducer的函数combination。

当使用combination的时候，combination会把所有子Reducer都执行一遍，子Reducer通过action.type匹配操作，因为是执行所有子Reducer，所以如果两个子Reducer匹配的action.type是一样的，那么都会成功匹配。




### 通过时间戳生成uid

```javascript
var date = new Date(),
    time = date.getTime();

var model = {
  inc: time,
  uid: function() {
    var new_id = 0;
    new_id += time;
    
    //自增
    this.inc++;
    new_id += this.inc;
    return new_id;
  }
}


for(var i = 0; i < 10; i++) {
  console.log(model.uid())
}
```


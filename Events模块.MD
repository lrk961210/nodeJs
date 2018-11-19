## Events模块
### 简介
回调函数模式让 Node 可以处理异步操作。但是，为了适应回调函数，异步操作只能有两个状态：开始和结束。
对于有多种状态的异步操作（状态1，状态2，状态3，……），回调函数无法处理，你得将异步操作拆开，分成多个阶段。每个阶段结束时，调用下一个回调函数。
为了解决这个问题，Node 提供 Event Emitter 接口。通过事件，解决多状态异步操作的响应问题

### 概述
Event Emitter 是一个接口，可以在任何对象上部署。这个接口由events模块提供。

#### 使用构造函数为 接口 直接生成 对象
```
  var EventEmitter = require('events').EventEmitter; 
  var emitter = new EventEmitter(); //调用构造函数，生成事件发生器实例emitter
```

###### 方法
on 监听事件（绑定回调函数）  emit 发出事件（触发回调函数，立即触发，即同步）
```
  emitter.on('事件1', function () {
    console.log('event has occured');
  });

  function f() {
    console.log('start');
    emitter.emit('事件1');
    console.log('end');
  }

  f()
  
  // start
  // event has occured
  // end
```

#### Event Emitter 接口的部署
Event Emitter 接口可以部署在任意对象上，使得这些对象也能订阅和发布消息。
```
  var EventEmitter = require('events').EventEmitter;

  function Dog(name) {
    this.name = name;
  }
  
  Dog.prototype.__proto__ = EventEmitter.prototype; // 未完待续 https://www.zhihu.com/question/34183746
  // 另一种写法
  // Dog.prototype = Object.create(EventEmitter.prototype);
  
  // 此时Dog 具有拥有了 EventEmitter 接口，他的
  var simon = new Dog('simon');

  simon.on('bark', function () {  // 绑定
    console.log(this.name + ' barked');
  });

  setInterval(function () {
    simon.emit('bark');           //触发
  }, 500);
```

##### Node 内置模块util的inherits方法，提供了另一种继承 Event Emitter 接口的方法。
```
  var util = require('util');
  var EventEmitter = require('events').EventEmitter;

  var Radio = function (station) {
      var self = this;

      setTimeout(function() {
        self.emit('open', station);    // 立即 触发open
      }, 0);
      setTimeout(function() {
        self.emit('close', station);   // 5s后 触发close
      }, 5000);

      this.on('newListener', function(listener) {   绑定 newListener
        console.log('Event Listener: ' + listener);
      });
  };

  util.inherits(Radio, EventEmitter); //继承 EventEmitter 接口
  module.exports = Radio;
```
使用上面继承 EventEmitter 接口 的 Radio
```
  var Radio = require('./radio.js');

  var station = {
    freq: '80.16',
    name: 'Rock N Roll Radio',
  };

  var radio = new Radio(station);
  // 绑定 open
  radio.on('open', function(station) {
    console.log('"%s" FM %s 打开', station.name, station.freq);
    console.log('♬ ♫♬');
  });
  //绑定close
  radio.on('close', function(station) {
    console.log('"%s" FM %s 关闭', station.name, station.freq);
  });
```

#### Event Emitter 的实例方法
  ```
  emitter.on(name, f) 对事件name指定监听函数f
  emitter.addListener(name, f) addListener == on (别名)
  emitter.once(name, f) 一次性监听
  emitter.listeners(name) 数组，事件name所有监听函数
  emitter.removeListener(name, f) 移除事件name的监听函数f
  emitter.removeAllListeners(name) 移除事件name的所有监听函数
```
触发事件，emit(事件名，参数1，参数2)； 返回一个 EventEmitter 对象

移除事件: removeListener(事件名， 回调参数名)
```
  var EventEmitter = require('events').EventEmitter;
  var emitter = new EventEmitter;

  emitter.on('message', console.log); //监听 message

  setInterval(function(){
    emitter.emit('message', 'foo bar');  //触发message
  }, 300);

  setTimeout(function(){
    emitter.removeListener('message', console.log); //移除message
  }, 1000);
```
使用 removeListener 方法模拟once方法
```
  var EventEmitter = require('events').EventEmitter;
  var emitter = new EventEmitter;

  function onlyOnce () {
    console.log("You'll never see this again");
    emitter.removeListener("firstConnection", onlyOnce); // 移除 事件
  }

  emitter.on("firstConnection", onlyOnce);  // 监听 事件
```

设置回调函数数量的最大值 setMaxListeners(int) 默认 10

移除事件 所有 的回调函数 removeAllListeners([事件])
注： 若不带参数，移除 所有事件 的回调函数

返回该事件所有回调函数 listeners(事件)
```
  var EventEmitter = require('events').EventEmitter;

  var ee = new EventEmitter;

  function onlyOnce () {
    console.log(ee.listeners("firstConnection"));
    ee.removeListener("firstConnection", onlyOnce);
    console.log(ee.listeners("firstConnection"));
  }

  ee.on("firstConnection", onlyOnce)
  ee.emit("firstConnection");
  ee.emit("firstConnection");

  // [ [Function: onlyOnce] ]
  // []
```

#### 错误捕获 
###### try...catch
```
  var EventEmitter = require('events').EventEmitter;
  var emitter = new EventEmitter();

  emitter.on('beep', function () {
    console.log('beep');
  });

  emitter.on('beep', function () {
    throw Error('oops!');
  });

  emitter.on('beep', function () {
    console.log('beep again');
  });

  console.log('before emit');

  try {
    emitter.emit('beep');
  } catch(err) {
    console.error('caught while emitting:', err.message); //捕获错误之后 不会 再调用之后的回调了
  }

  console.log('after emit');
  
  //before emit
  //beep
  //caught while emitting: oops!  
  //after emit
```

###### 让进程 监听 uncaughtException 事件
```
  process.on('uncaughtException', function (err) {
    console.error('uncaught exception:', err.stack || err);
    // 关闭资源
    closeEverything(function(err) {
      if (err)
        console.error('Error while closing everything:', err.stack || err);
      // 退出进程
      process.exit(1);
    });
  });
```

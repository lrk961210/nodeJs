## 1、简介
Node应用由模块构成，而模块使用CommonJS模块规范

#### 每个文件就是一个模块，由自己的作用域。
对于自己内部定义的变量、函数，都是私有的，对其他文件不可见
```
  var x = 2;
  var getX = function () {
    return x;
  };
```

如果想在文件之间分享变量，要设置global对象的属性（不推荐）
```
global.aaa = true;
```

#### module.exports
CommonJS 规定，module代表当前模块，module.exports 是对外的接口。
加载某个模块 == 加载 module.exports 属性
```
  var x = 2;
  var getX = function () {
    return x;
  };
  
  //对外暴露变量
  module.exports.x = x; 
  module.exports.addX = addX;
```

#### require 加载模块
```
  var aaa = require('./aaa.js');

  console.log(aaa.x); // 2
  console.log(aaa.getX()); // 2
```
参考链接 http://javascript.ruanyifeng.com/nodejs/module.html  
未完待续


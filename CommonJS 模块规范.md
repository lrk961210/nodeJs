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

## require命令(读入并执行js文件，返回 exports对象)
```
    // example.js
    var invisible = function () { // 内部函数，不可见
      console.log("invisible");
    }

    exports.message = "hi";  // 导出变量

    exports.say = function () { // 导出函数
      console.log(message);
    }
```
如果模块输出的是一个函数，那就不能定义在 exports 对象上面，而要定义在 module.exports 变量上面。
```
    module.exports = function () {  // exports 直接 等于函数
      console.log("hello world")
    }

    require('./example2.js')() // 调用自身

```

#### 加载规则
require命令用于加载文件，后缀名默认为.js。
```
    var foo = require('foo');
    //  等同于
    var foo = require('foo.js');
```
寻路规则
/  开头： 绝对路径查找
./ 开头: 相对路径查找
不以 ./ 或 / 开头 ： 加载的是一个默认提供的核心模块（node_modules）。依次搜索： 
```
  // 脚本 /home/user/projects/foo.js 执行了 require('bar.js') 命令
  /usr/local/lib/node/bar.js     // 全局安装的
  /home/user/projects/node_modules/bar.js // 各级node_modules目录的已安装模块
  /home/user/node_modules/bar.js
  /home/node_modules/bar.js
  /node_modules/bar.js
```

不以“./“或”/“开头，而且是一个 路径  require('example-module/path/to/file')：
先找example-module的位置，然后再以它为参数，找到后续路径

如果指定的模块文件没有发现，Node会尝试为文件名添加.js、.json、.node后，再去搜索。
.js 以文本格式的JavaScript脚本文件解析，.json 以JSON格式的文本文件解析，.node 以编译后的二进制文件解析。

require.resolve() 得到require命令加载的确切文件名

#### 目录的加载规则
为目录设置一个入口文件，require通过入口文件， 加载目录：
1、package.json， 入口文件写入main字段
```
    // package.json
    { "name" : "some-library",
      "main" : "./lib/some-library.js" }
```

2、没有 package.json / 没有main字段, 加载目录下的index.js或index.node

#### 模块的缓存
第一次加载某个模块时，Node会缓存该模块。
以后再加载该模块，就直接从缓存取出该模块的module.exports属性。

```
  require('./example.js');
  require('./example.js').message = "hello";
  require('./example.js').message
  // "hello"
```
如果想多次执行某个模块，可以让该模块输出一个函数，每次require这个模块，重新执行一下输出的函数。

删除缓存
 ```
    // 删除指定模块的缓存
    delete require.cache[moduleName];

    // 删除所有模块的缓存
    Object.keys(require.cache).forEach(function(key) {
      delete require.cache[key];
    })
 ```
注： 同样的模块名，不同的路径，require命令会重新加载该模块。

#### 环境变量NODE_PATH
Node执行一个脚本时，会先查看环境变量NODE_PATH。
它是一组以冒号分隔的绝对路径。在其他位置找不到指定模块时，Node会去这些路径查找。

遇到复杂的相对路径, 如var myModule = require('../../../../lib/myModule');
1、加入node_modules目录
2、修改NODE_PATH环境变量，package.json文件采用下面的写法。（历史遗留下来的一个路径解决方案，不推荐）
```
    {
      "name": "node_path",
      "version": "1.0.0",
      "description": "",
      "main": "index.js",
      "scripts": {
        "start": "NODE_PATH=lib node index.js"
      },
      "author": "",
      "license": "ISC"
    }
```

#### 模块循环加载

#### require.main === module // 判断是直接执行，还是被调用执行


## 模块的加载机制


参考链接 http://javascript.ruanyifeng.com/nodejs/module.html  
未完待续


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

#### node 的 module 对象
Node内部有一个Module构建函数。所有模块都是Module的实例。
每个模块的内部，都有一个module对象， 代表当前模块，有以下属性

```
  module.id 模块的识别符，通常是带有绝对路径的模块文件名。
  module.filename 模块的文件名，带有绝对路径。
  module.loaded 返回一个布尔值，模块是否已经完成加载。
  module.parent 返回一个对象，调用该模块的模块。
  module.children 返回一个数组，该模块要用到的其他模块。
  module.exports 模块对外输出的值。
``` 

示例： 
  ```
  // example.js
  var jquery = require('jquery');
  exports.$ = jquery;
  console.log(module);
```
执行这个文件，命令行会输出如下信息。
```
  { id: '.',
    exports: { '$': [Function] }
    parent: null,    //在命令行中调用，所以为null
    filename: '/path/to/example.js',   //绝对路径
    loaded: false,   //还未完成加载
    children:  //调用到 jquery （开头 require）
     [ { id: '/path/to/node_modules/jquery/dist/jquery.js',
         exports: [Function],
         parent: [Circular],
         filename: '/path/to/node_modules/jquery/dist/jquery.js',
         loaded: true,
         children: [],
         paths: [Object] } ],
    paths: //这个好像没说明
     [ '/home/user/deleted/node_modules',
       '/home/user/node_modules',
       '/home/node_modules',
       '/node_modules' ]
  }
```

可以利用parent是否为null，来判断是否为入口脚本
```
  if (!module.parent) {
      // ran with `node something.js`
      app.listen(8088, function() {
          console.log('app listening on port 8088');
      })
  } else {
      // used with `require('/.something.js')`
      module.exports = app;
  }
```

### module.exports
module.exports属性表示当前模块对外输出的接口，其他文件加载该模块，实际上就是读取module.exports变量。






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
CommonJS模块的加载机制是，输入的是被输出的值的拷贝。一旦输出一个值，模块内部的变化就影响不到这个值。
```
  // lib.js
  var counter = 3;
  function incCounter() {
    counter++;
  }
  module.exports = {
    counter: counter,
    incCounter: incCounter,
  };
```

```
    // main.js
    var counter = require('./lib').counter;
    var incCounter = require('./lib').incCounter;

    console.log(counter);  // 3
    incCounter();
    console.log(counter); // 3
```

#### require的内部处理流程
require命令是CommonJS规范之中，用来加载其他模块的命令。
它其实不是一个全局命令，而是指向当前模块的module.require命令，
而后者又调用Node的内部命令Module._load
```
    Module._load = function(request, parent, isMain) {
      // 1. 检查 Module._cache，是否缓存之中有指定模块
      // 2. 如果缓存之中没有，就创建一个新的Module实例
      // 3. 将它保存到缓存
      // 4. 使用 module.load() 加载指定的模块文件，
      //    读取文件内容之后，使用 module.compile() 执行文件代码
      // 5. 如果加载/解析过程报错，就从缓存删除该模块
      // 6. 返回该模块的 module.exports
    };
```
上面的第4步，采用module.compile()执行指定模块的脚本，逻辑如下。
```
    Module.prototype._compile = function(content, filename) {
      // 1. 生成一个require函数，指向module.require
      // 2. 加载其他辅助方法到require
      // 3. 将文件内容放到一个函数之中，该函数可调用 require
      // 4. 执行该函数
    };
```
上面的第1步和第2步，require函数及其辅助方法主要如下。
```
    require(): 加载外部模块
    require.resolve()：将模块名解析到一个绝对路径
    require.main：指向主模块
    require.cache：指向所有缓存的模块
    require.extensions：根据文件的后缀名，调用不同的执行函数
```
一旦require函数准备完毕，整个所要加载的脚本内容，就被放到一个新的函数之中，
这样可以避免污染全局环境。该函数的参数包括require、module、exports，以及其他一些参数。
```
    (function (exports, require, module, __filename, __dirname) {
      // YOUR CODE INJECTED HERE!
    });
```
Module._compile方法是同步执行的，所以Module._load要等它执行完成，才会向用户返回module.exports的值。


参考链接 http://javascript.ruanyifeng.com/nodejs/module.html  
未完待续


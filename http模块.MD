## 基本用法
http模块主要用于搭建HTTP服务。
```
    // app.js
    var http = require('http'); // 加载http模块
    
    // 使用createServer方法，创造服务器实例
    // http.createServer(function(request, response){})
    
    http.createServer(function (request, response){     
      response.writeHead(200, {'Content-Type': 'text/plain'}); //写入HTTP回应的头信息
      response.write("Hello World");
      response.end();                 // 写入HTTP回应的具体内容，以及回应完成后关闭本次对话
    }).listen(8080, '127.0.0.1');     // 启动服务器实例，监听本机的8080端口

    console.log('Server running on port 8080.');

    // 另一种写法
    function onRequest(request, response) {
      response.writeHead(200, {"Content-Type": "text/plain"});
      response.write("Hello World");
      response.end();
    }

    http.createServer(onRequest).listen(process.env.PORT);

```

#### 执行脚本
```
  $ node app.js
```

#### 使用 fs 模块读取文件，用于返回
```
    var http = require('http');
    var fs = require('fs');   // 加载 fs 模块

    http.createServer(function (request, response){
      // 读取文件， 写入http回应
      fs.readFile('data.txt', function readData(err, data) {
        response.writeHead(200, {'Content-Type': 'text/plain'});
        response.end(data);
      });

      // 或者
      
      fs.createReadStream(`${__dirname}/index.html`).pipe(response);
    }).listen(8080, '127.0.0.1');

    console.log('Server running on port 8080.');

```

#### 根据不同网址的请求，显示不同的内容（路由）
```
    var http = require('http');

    http.createServer(function(req, res) {

      // 主页
      if (req.url == "/") {
        res.writeHead(200, { "Content-Type": "text/html" });
        res.end("Welcome to the homepage!");
      }

      // About页面
      else if (req.url == "/about") {
        res.writeHead(200, { "Content-Type": "text/html" });
        res.end("Welcome to the about page!");
      }

      // 404错误
      else {
        res.writeHead(404, { "Content-Type": "text/plain" });
        res.end("404 error! File not found.");
      }

    }).listen(8080, "localhost");

```

## request 对象
createServer函数中的 request 对象，拥有以下属性。

- url：发出请求的网址。
- method：HTTP请求的方法。
- headers：HTTP请求的所有HTTP头信息。

获取请求的路径名。
```
    var url = require('url');
    var pathname = url.parse(request.url).pathname;
```

setEncoding()方法用于设置请求的编码
```
    request.setEncoding("utf8");
```

addListener()方法用于为请求添加监听事件的回调函数
```
    var querystring = require('querystring');
    var postData = '';

    request.addListener('data', function (postDataChunk) {
      postData += postDataChunk;
    });

    request.addListener('end', function () {
      response.writeHead(200, {'Content-Type': 'text/plain'});
      response.write("You've sent the text: " + querystring.parse(postData).text);
      response.end();
    });
```
#### 处理异步操作
```
    var exec = require("child_process").exec;

    exec('ls -lah', function (error, stdout, stderr) {
      response.writeHead(200, {'Content-Type': 'text/plain'});
      response.write(stdout);
      response.end();
    });
```


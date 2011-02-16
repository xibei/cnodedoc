## Synopsis 概要

An example of a [web server](http.html) written with Node which responds with 'Hello
World':

下边是一个用Node编写的对所有请求简单返回'Hello World‘的[web服务器](http.html)例子：

    var http = require('http');

    http.createServer(function (request, response) {
      response.writeHead(200, {'Content-Type': 'text/plain'});
      response.end('Hello World\n');
    }).listen(8124);

    console.log('Server running at http://127.0.0.1:8124/');

To run the server, put the code into a file called `example.js` and execute
it with the node program

要运行这个服务器程序，只要将上述代码保存为文件`example.js`并用node程序执行此文件：

    > node example.js
    Server running at http://127.0.0.1:8124/

All of the examples in the documentation can be run similarly.

此文档中所有例子均可用同样的方法运行。
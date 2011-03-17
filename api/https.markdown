## HTTPS

HTTPS is the HTTP protocol over TLS/SSL. In Node this is implemented as a
separate module.

HTTPS是基于TLS（Transport Layer Security 传输层安全/SSL（Secure Sockets Layer 安全套接层）的HTTP协议，在Node中，它作为一个独立的模块被实现

## https.Server
## https.createServer

Example:

    // curl -k https://localhost:8000/
    var https = require('https');
    var fs = require('fs');

    var options = {
      key: fs.readFileSync('test/fixtures/keys/agent2-key.pem'),
      cert: fs.readFileSync('test/fixtures/keys/agent2-cert.pem')
    };

    https.createServer(options, function (req, res) {
      res.writeHead(200);
      res.end("hello world\n");
    }).listen(8000);

这个例子演示如何创建基于HTTPS的服务器，options对象中的KEY为对应的钥匙文件，cert为对应的证书类型，
在Node中，钥匙、CA信息都是用pem格式的文件保存的

## https.request(options, callback)

Makes a request to a secure web server.
Similar options to `http.request()`.

向安全的web服务器发送请求，可选参数和`http.request()`类似

Example:

    var https = require('https');

    var options = {
      host: 'encrypted.google.com',
      port: 443,
      path: '/',
      method: 'GET'
    };

    var req = https.request(options, function(res) {
      console.log("statusCode: ", res.statusCode);
      console.log("headers: ", res.headers);

      res.on('data', function(d) {
        process.stdout.write(d);
      });
    });
    req.end();

    req.on('error', function(e) {
      console.error(e);
    });

## https.get(options, callback)

Like `http.get()` but for HTTPS.

类似`http.get()`但它基于HTTPS协议

Example:

    var https = require('https');

    https.get({ host: 'encrypted.google.com', path: '/' }, function(res) {
      console.log("statusCode: ", res.statusCode);
      console.log("headers: ", res.headers);

      res.on('data', function(d) {
        process.stdout.write(d);
      });

    }).on('error', function(e) {
      console.error(e);
    });

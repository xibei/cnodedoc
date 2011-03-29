## HTTPS HTTPS模块

HTTPS is the HTTP protocol over TLS/SSL. In Node this is implemented as a
separate module.

HTTPS是基于TLS（Transport Layer Security 传输层安全）/SSL（Secure Sockets Layer 安全套接层）的HTTP协议，在Node中，它作为一个独立的模块被实现

## https.Server
## https.createServer

Example:

例子：

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


## https.request(options, callback)

Makes a request to a secure web server.
Similar options to `http.request()`.

向安全的web服务器发送请求，可选参数和`http.request()`类似。

Example:

例子：

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

The options argument has the following options

options参数可包含以下内容：

- host: IP or domain of host to make request to. Defaults to `'localhost'`.
  host: 要访问的主机的IP地址或域名。默认为`'localhost'`。
- port: port of host to request to. Defaults to 443.
  port: 要访问的主机端口。默认为433。
- path: Path to request. Default `'/'`.
  path: 要访问的路径。默认为`'/'`。
- method: HTTP request method. Default `'GET'`.
  method: HTTP请求方式。默认为`'GET'`。
- key: Private key to use for SSL. Default `null`.
  key: SSL所使用的私钥。默认为`null`。
- cert: Public x509 certificate to use. Default `null`.
  cert: 所使用的x509公钥证书。默认为`null`。
- ca: An authority certificate or array of authority certificates to check
  the remote host against.
  ca: 用于验证远程主机身份的一个认证中心证书（或多个认证中心证书数组）。


## https.get(options, callback)

Like `http.get()` but for HTTPS.

类似`http.get()`但它基于HTTPS协议。

Example:

例子：

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

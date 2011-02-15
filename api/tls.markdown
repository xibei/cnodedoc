## TLS (SSL) 传输层安全（安全套接层）模块

Use `require('tls')` to access this module.

使用`require('tls')`访问此模块。

The `tls` module uses OpenSSL to provide Transport Layer Security and/or
Secure Socket Layer: encrypted stream communication.

`tls`模块使用OpenSSL提供传输层安全协议和（或）安全套接层协议：加密的
流通信。

TLS/SSL is a public/private key infrastructure. Each client and each
server must have a private key. A private key is created like this

TLS/SSL基于公钥/私钥的非对称加密体系，每一个客户端与服务器都需要拥有
一个私有密钥。私有密钥可用如下方式生成：

    openssl genrsa -out ryans-key.pem 1024

All severs and some clients need to have a certificate. Certificates are public
keys signed by a Certificate Authority or self-signed. The first step to
getting a certificate is to create a "Certificate Signing Request" (CSR)
file. This is done with:

所有服务器和某些客户端需要拥有一份数字证书。数字证书是由某个证书授权
中心使用其公钥签名授予的，或者也可以是自签名的。要获得一份数字证书，首
先需要生成一个“证书签名请求”（CSR）文件。方法如下：

    openssl req -new -key ryans-key.pem -out ryans-csr.pem

To create a self-signed certificate with the CSR, do this:

要使用CSR文件生成一个自签名的数字证书，方法如下：

    openssl x509 -req -in ryans-csr.pem -signkey ryans-key.pem -out ryans-cert.pem

Alternatively you can send the CSR to a Certificate Authority for signing.

你也可以将CSR文件发给一家证书授权中心以获得签名。

(TODO: docs on creating a CA, for now interested users should just look at
`test/fixtures/keys/Makefile` in the Node source code)

（有待补充：关于如何创建证书授权中心的文档。感兴趣的用户可以直接浏览Node源代码中的`test/fixtures/keys/Makefile`文件）


### s = tls.connect(port, [host], [options], callback)

Creates a new client connection to the given `port` and `host`. (If `host`
defaults to `localhost`.) `options` should be an object which specifies

建立一个到指定端口`port`和主机`host`的新的客户端连接。（`host`参数
的默认值为`localhost`。）`options`是一个包含以下内容的对象：

  - `key`: A string or `Buffer` containing the private key of the server in
    PEM format. (Required)

  `key`：包含服务器私有密钥的字符串或`Buffer`缓冲对象。密钥的格式
为PEM。（必选）

  - `cert`: A string or `Buffer` containing the certificate key of the server in
    PEM format.

  `cert`：包含服务器数字证书密钥的字符串或`Buffer`缓冲对象。密钥的
格式为PEM。

  - `ca`: An array of strings or `Buffer`s of trusted certificates. If this is
    omitted several well known "root" CAs will be used, like VeriSign.
    These are used to authorize connections.

  `ca`：包含可信任数字证书字符串或`Buffer`缓冲对象的数组。如果忽略
此属性，则会使用几个常见的"根"证书授权中心，如VeriSign。这些证书
将被用来对连接进行认证。

`tls.connect()` returns a cleartext `CryptoStream` object.

`tls.connect()`返回一个明文的`CryptoStream`对象。

After the TLS/SSL handshake the `callback` is called. The `callback` will be
called no matter if the server's certificate was authorized or not. It is up
to the user to test `s.authorized` to see if the server certificate was
signed by one of the specified CAs. If `s.authorized === false` then the error
can be found in `s.authorizationError`.

TLS/SSL连接握手之后`callback`回调函数会被调用。无论服务器的数字证书
是否通过认证，`callback`函数都会被调用。用户应该检查`s.authorized`以确认
服务器数字证书是否被指定的证书授权中心签名。当`s.authorized === false`
时可以从`s.authorizationError`中获得具体的错误。


### tls.Server

This class is a subclass of `net.Server` and has the same methods on it.
Instead of accepting just raw TCP connections, this accepts encrypted
connections using TLS or SSL.

这是`net.Server`的子类，拥有和`net.Server`完全一样的方法。区别在于这个
类使用TLS或SSL建立加密的连接，而非仅仅接受原始的TCP连接。

Here is a simple example echo server:

下面是一个简单的回声服务器的例子：

    var tls = require('tls');
    var fs = require('fs');

    var options = {
      key: fs.readFileSync('server-key.pem'),
      cert: fs.readFileSync('server-cert.pem')
    };

    tls.createServer(options, function (s) {
      s.write("welcome!\n");
      s.pipe(s);
    }).listen(8000);


You can test this server by connecting to it with `openssl s_client`:

你可以使用`openssl s_client`连接到这个服务器进行测试：


    openssl s_client -connect 127.0.0.1:8000


#### tls.createServer(options, secureConnectionListener)

This is a constructor for the `tls.Server` class. The options object
has these possibilities:

这是`tls.Server`类的构造函数。参数options对象可以包含下列内容：

  - `key`: A string or `Buffer` containing the private key of the server in
    PEM format. (Required)

  `key`：包含服务器私有密钥的字符串或`Buffer`缓冲对象。密钥的格式
为PEM。（必选）

  - `cert`: A string or `Buffer` containing the certificate key of the server in
    PEM format. (Required)

  `cert`：包含服务器数字证书密钥的字符串或`Buffer`缓冲对象。密钥的
格式为PEM。（必选）

  - `ca`: An array of strings or `Buffer`s of trusted certificates. If this is
    omitted several well known "root" CAs will be used, like VeriSign.
    These are used to authorize connections.

  `ca`：包含可信任数字证书字符串或`Buffer`缓冲对象的数组。如果忽略
此属性，则会使用几个常见的"根"证书授权中心，如VeriSign。这些证书
将被用来对连接进行认证。

  - `requestCert`: If `true` the server will request a certificate from
    clients that connect and attempt to verify that certificate. Default:
    `false`.

  `requestCert`：如果设为`true`则服务器会向建立连接的客户端要求
一个数字证书，并且试图去验证这份数字证书。默认为`false`。

  - `rejectUnauthorized`: If `true` the server will reject any connection
    which is not authorized with the list of supplied CAs. This option only
    has an effect if `requestCert` is `true`. Default: `false`.

  `rejectUnauthorized`：如果设为`true`则服务器将拒绝任何没有通过可
信任证书授权中心验证的连接。此选项仅在`requestCert`设为`true`时有效。
默认为`false`。


#### Event: 'secureConnection' 事件：'secureConnection'

`function (cleartextStream) {}`

This event is emitted after a new connection has been successfully
handshaked. The argument is a duplex instance of `stream.Stream`. It has all
the common stream methods and events.

当一个新的连接成功完成握手过程后此事件被激发。参数是一个双工的
`stream.Stream`实例对象，此对象具有流对象的所有公共的方法和事件。

`cleartextStream.authorized` is a boolean value which indicates if the
client has verified by one of the supplied cerificate authorities for the
server. If `cleartextStream.authorized` is false, then
`cleartextStream.authorizationError` is set to describe how authorization
failed. Implied but worth mentioning: depending on the settings of the TLS
server, you unauthorized connections may be accepted.


#### server.listen(port, [host], [callback])

Begin accepting connections on the specified `port` and `host`.  If the
`host` is omitted, the server will accept connections directed to any
IPv4 address (`INADDR_ANY`).

This function is asynchronous. The last parameter `callback` will be called
when the server has been bound.

See `net.Server` for more information.


#### server.close()

Stops the server from accepting new connections. This function is
asynchronous, the server is finally closed when the server emits a `'close'`
event.


#### server.maxConnections

Set this property to reject connections when the server's connection count gets high.

#### server.connections

The number of concurrent connections on the server.

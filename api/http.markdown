## HTTP

To use the HTTP server and client one must `require('http')`.

如果要使用HTTP的server以及client模块则必须使用require('http')加载http模块 

The HTTP interfaces in Node are designed to support many features
of the protocol which have been traditionally difficult to use.
In particular, large, possibly chunk-encoded, messages. The interface is
careful to never buffer entire requests or responses--the
user is able to stream data.

NODE中的HTTP接口被设计成为支持HTTP协议的很多特性，这些特性通常那难以掌控，特别是 large,possible chunk-encoded(块编码),messages。 
这个接口特意不缓冲整个请求(request)或者响应(responses)使用户可以使用流的形式操作数据。 

HTTP message headers are represented by an object like this:

以下是用对象的形式表示的HTTP信息头 

    { 'content-length': '123',
      'content-type': 'text/plain',
      'connection': 'keep-alive',
      'accept': '*/*' }

Keys are lowercased. Values are not modified.

所有key都是小写，数值不能被修改 

In order to support the full spectrum of possible HTTP applications, Node's
HTTP API is very low-level. It deals with stream handling and message
parsing only. It parses a message into headers and body but it does not
parse the actual headers or the body.

为了支持尽可能多的HTTP应用，NODE的HTTP API非常底层。 其只处理到流(stream)相关的操作以及信息解析。
API将信息解析成为信息头和信息体，但并不解析实际的信息头和信息体的具体内容。

## http.Server

This is an `EventEmitter` with the following events:

此模块会触发以下事件 

### Event: 'request'

`function (request, response) { }`

 `request` is an instance of `http.ServerRequest` and `response` is
 an instance of `http.ServerResponse`
 
`request`是`http.ServerRequest`的一个实例，而`response`是`http.ServerResponse`的一个实例
 
### Event: 'connection'

`function (stream) { }`

 When a new TCP stream is established. `stream` is an object of type
 `net.Stream`. Usually users will not want to access this event. The
 `stream` can also be accessed at `request.connection`.
 
当一个新的TCP stream建立后发出此消息。stream是一个net.Stream的对象，
通常用户不会访问/使用这个事件。参数stream也可以在request.connection中访问到. 

### Event: 'close'

`function (errno) { }`

 Emitted when the server closes.
 
当服务器关闭的时候触发此事件。 

### Event: 'request'

`function (request, response) {}`

Emitted each time there is request. Note that there may be multiple requests
per connection (in the case of keep-alive connections).

每个请求发生的时候均会被触发。请记住，每个连接可能会有多个请求(在keep-alive连接情况下) 

### Event: 'checkContinue'

`function (request, response) {}`

Emitted each time a request with an http Expect: 100-continue is received.
If this event isn't listened for, the server will automatically respond
with a 100 Continue as appropriate.

每当请求期望接收到100-continue时派发，如果该事件未被监听，服务器会自动
返回100-continue给客户端。

Handling this event involves calling `response.writeContinue` if the client
should continue to send the request body, or generating an appropriate HTTP
response (e.g., 400 Bad Request) if the client should not continue to send the
request body.

该事件的处理涉及两种情况，如果客户端应当继续发送请求体，那么需要调用response.writeContinue，
而如果客户端不应该继续发送请求正文，那么应该产生一个适当的HTTP反应（如400错误请求）。

Note that when this event is emitted and handled, the `request` event will
not be emitted.

注意如果该事件被派发并处理的话，那么将不再派发`request`事件。

### Event: 'upgrade'

`function (request, socket, head)`

Emitted each time a client requests a http upgrade. If this event isn't
listened for, then clients requesting an upgrade will have their connections
closed.

每当一个客户端请求一个http upgrade时候发出此消息。如果这个事件没有监听，
那么请求upgrade的客户端对应的连接将被关闭。 

* `request` is the arguments for the http request, as it is in the request event.
参数“request”代表一个http请求，和'request'事件的参数意义相同。

* `socket` is the network socket between the server and client.
socket是在服务器与客户端之间连接用的网络socket 

* `head` is an instance of Buffer, the first packet of the upgraded stream, this may be empty.
head是Buffer的一个实例,是upgraded stream(升级版stream....应当就是http upgrade)所发出的第一个包，这个参数可以为空。 

After this event is emitted, the request's socket will not have a `data`
event listener, meaning you will need to bind to it in order to handle data
sent to the server on that socket.

当此事件被触发后，该请求所使用的socket并不会有一个数据事件的监听者,这意味着你如果需要
处理通过这个SOCKET发送到服务器端的数据的话则需要自己绑定数据事件监听器 

### Event: 'clientError'

`function (exception) {}`

If a client connection emits an 'error' event - it will forwarded here.

当客户端连接出现错误时会触发'error'事件

### http.createServer(requestListener)

Returns a new web server object.

返回一个新的web server对象。 

The `requestListener` is a function which is automatically
added to the `'request'` event.

requestListener监听器会自动添加到`'request'`事件中

### server.listen(port, [hostname], [callback])

Begin accepting connections on the specified port and hostname.  If the
hostname is omitted, the server will accept connections directed to any
IPv4 address (`INADDR_ANY`).

在指定端口和主机名上接受连接。如果hostname没有写,这个服务器将直接在此机器的
所有IPV4地址上接受连接(INADDR_ANY). 

To listen to a unix socket, supply a filename instead of port and hostname.

如果要在UNIX SOCKET上监听的话，则需要提供一个文件名来替换端口和主机名. 

This function is asynchronous. The last parameter `callback` will be called
when the server has been bound to the port.

这个方法是一个异步的方法，作为最后一个参数的回调方法将在服务器已经在此端口上绑定好后被调用. 

### server.listen(path, [callback])

Start a UNIX socket server listening for connections on the given `path`.

建立一个UNIX SOCKET服务器并在指定路径监听。 

This function is asynchronous. The last parameter `callback` will be called
when the server has been bound.

这个方法是一个异步的方法，作为最后一个参数的回调方法将在服务器已经在此端口上绑定好后被调用。 

### server.close()

使此服务器停止接受任何新连接。 

## http.ServerRequest

This object is created internally by a HTTP server -- not by
the user -- and passed as the first argument to a `'request'` listener.

这个对象通常由HTTP SERVER建立而非用户手动建立，并且会作为传递给 'request'事件监听器第一个参数

This is an `EventEmitter` with the following events:

此对象的可以触发以下事件： 

### Event: 'data'

`function (chunk) { }`

Emitted when a piece of the message body is received.

当接收到信息体中的一部分时候会发出data事件。 

Example: A chunk of the body is given as the single
argument. The transfer-encoding has been decoded.  The
body chunk is a string.  The body encoding is set with
`request.setBodyEncoding()`.

例如:代表消息体的数据块将作为唯一的参数传递给回调函数。这个时候数据已经按照传输编码
进行了解码（不是字符集编码）。消息体本身是一个字符串，可以使用request.setBodyEncoding()方法设定消息体的编码。 

### Event: 'end'

`function () { }`

Emitted exactly once for each message. No arguments.  After
emitted no other events will be emitted on the request.

每次完全接收完信息后都会触发一次。没有参数，当这个事件发出后，将不会再触发其他事件。 

### request.method

The request method as a string. Read only. Example:
`'GET'`, `'DELETE'`.

request.method是一个只读字符串。例如'GET','DELETE' 

### request.url

Request URL string. This contains only the URL that is
present in the actual HTTP request. If the request is:

代表所请求的URL字符串.他仅包括实际的HTTP请求中的URL地址。如果这个请求是 

    GET /status?name=ryan HTTP/1.1\r\n
    Accept: text/plain\r\n
    \r\n

Then `request.url` will be:

则request.url 应当是 

    '/status?name=ryan'

If you would like to parse the URL into its parts, you can use
`require('url').parse(request.url)`.  Example:

如果你想要解析这个URL中的各个部分，你应当使用 require('url').parse(request.url). 

    node> require('url').parse('/status?name=ryan')
    { href: '/status?name=ryan',
      search: '?name=ryan',
      query: 'name=ryan',
      pathname: '/status' }

If you would like to extract the params from the query string,
you can use the `require('querystring').parse` function, or pass
`true` as the second argument to `require('url').parse`.  Example:

如果你想从查询字符串中提出这些参数，你可以使用require('querystring').parse方法,
或者传一个true作为第二个参数给require('url').parse方法。 

    node> require('url').parse('/status?name=ryan', true)
    { href: '/status?name=ryan',
      search: '?name=ryan',
      query: { name: 'ryan' },
      pathname: '/status' }



### request.headers

Read only.

只读。

### request.trailers

Read only; HTTP trailers (if present). Only populated after the 'end' event.

只读，HTTP尾部（如果存在的话），只有在end事件派发后该值才会被填充。

### request.httpVersion

The HTTP protocol version as a string. Read only. Examples:
`'1.1'`, `'1.0'`.
Also `request.httpVersionMajor` is the first integer and
`request.httpVersionMinor` is the second.

以字符串形式表示HTTP协议版本。例如'1.1','1.0'。request.httpVersionMajor对应版本号的第一个数字，
request.httpVersionMinor则对应第二个数字。 

### request.setEncoding(encoding=null)

Set the encoding for the request body. Either `'utf8'` or `'binary'`. Defaults
to `null`, which means that the `'data'` event will emit a `Buffer` object..

设置此请求的包体的字集编码,'utf8'或者'binary'。缺省值是null，这表示'data'事件的参数将会是一个Buffer对象。 

### request.pause()

Pauses request from emitting events.  Useful to throttle back an upload.

暂停此request触发事件.对于控制上传非常有用。 

### request.resume()

Resumes a paused request.

恢复一个暂停的request。 

### request.connection

The `net.Stream` object associated with the connection.

request.connection是一个代表当前连接的net.Stream对象。 

With HTTPS support, use request.connection.verifyPeer() and
request.connection.getPeerCertificate() to obtain the client's
authentication details.

对于HTTPS，使用request.connection.verifyPeer() 和 request.connection.getPeerCertificate()来获得客户端的认证详情。 

## http.ServerResponse

This object is created internally by a HTTP server--not by the user. It is
passed as the second parameter to the `'request'` event. It is a `Writable Stream`.

这个对象一般由HTTP服务器建立而非用户自己手动建立。它作为'request'事件的第二个参数，这是一个可写流。

### response.writeContinue()

Sends a HTTP/1.1 100 Continue message to the client, indicating that
the request body should be sent. See the the `checkContinue` event on
`Server`.

发送HTTP/1.1 100 Continue消息给客户端，说明请求的消息体将要被发送，参见服务器`Server`中的`checkContinue`事件。

### response.writeHead(statusCode, [reasonPhrase], [headers])

Sends a response header to the request. The status code is a 3-digit HTTP
status code, like `404`. The last argument, `headers`, are the response headers.
Optionally one can give a human-readable `reasonPhrase` as the second
argument.

这个方法的是用来发送一个响应报文头给本次的请求方，第一个参数状态码是由一个3位数字所构成的HTTP状态，
比如404之类的。最后一个参数headers是响应头具体内容.也可以使用一个方便人们直观了解的reasonPhrase作为第二个参数。 

Example:

    var body = 'hello world';
    response.writeHead(200, {
      'Content-Length': body.length,
      'Content-Type': 'text/plain' });

This method must only be called once on a message and it must
be called before `response.end()` is called.

在一次完整信息交互中此方法只能调用一次，并且必须在调用response.end()之前调用。 

If you call `response.write()` or `response.end()` before calling this, the
implicit/mutable headers will be calculated and call this function for you.

如果你在`response.write()` 或者 `response.end()`之后调用writeHead，那么将返回隐式的头部信息且是不可预期的。

### response.statusCode

When using implicit headers (not calling `response.writeHead()` explicitly), this property
controlls the status code that will be send to the client when the headers get
flushed.

如果没有显示指明消息头部信息（没有明确调用），这个属性将控制当头部刷新时返回给客户端的状态码。

Example:

例如：

    response.statusCode = 404;

### response.setHeader(name, value)

Sets a single header value for implicit headers.  If this header already exists
in the to-be-sent headers, it's value will be replaced.  Use an array of strings
here if you need to send multiple headers with the same name.

显示设置头部信息参数，如果已经设置了对应的头部信息，那么该值将被替换，如果你想发送相同名字的多个头部信息，
可以使用字符串数组的形式设置

Example:

例如：

    response.setHeader("Content-Type", "text/html");

or

    response.setHeader("Set-Cookie", ["type=ninja", "language=javascript"]);


### response.getHeader(name)

Reads out a header that's already been queued but not sent to the client.  Note
that the name is case insensitive.  This can only be called before headers get
implicitly flushed.

读取尚未发送给客户端且已经排列好的的头部信息，注意参数名不区分大小写，该方法只有在
头部信息没有隐式刷新前调用。

Example:

    var contentType = response.getHeader('content-type');

### response.removeHeader(name)

Removes a header that's queued for implicit sending.

移除隐式发送的头部信息。

Example:

    response.removeHeader("Content-Encoding");


### response.write(chunk, encoding='utf8')

If this method is called and `response.writeHead()` has not been called, it will
switch to implicit header mode and flush the implicit headers.

如果在`response.writeHead()`没有调用的时候调用该函数，将会切换头部信息的模式并刷新头部信息。 

This sends a chunk of the response body. This method may
be called multiple times to provide successive parts of the body.

它负责发送响应报文中的部分数据，如果要发送一个报文体的多个部分，则可以多次调用此方法。 

`chunk` can be a string or a buffer. If `chunk` is a string,
the second parameter specifies how to encode it into a byte stream.
By default the `encoding` is `'utf8'`.

参数chunk可以是一个字符串或者一个buffer。如果chunk是一个字符串，则第二个参数指定如何将
这个字符串编码成字节流，缺省情况下，编码为'utf8'。 

**Note**: This is the raw HTTP body and has nothing to do with
higher-level multi-part body encodings that may be used.

注意:这是一个原始格式http报文体，和高层协议中的多段消息体编码格式({'Transfer-Encoding':'chunked'})无关。 

The first time `response.write()` is called, it will send the buffered
header information and the first body to the client. The second time
`response.write()` is called, Node assumes you're going to be streaming
data, and sends that separately. That is, the response is buffered up to the
first chunk of body.

第一次调用response.write()时，此方法会将已经缓冲的消息头和第一块消息体发送给客户。 
当第二次调用response.write()的时候，node将假定你想要以流的形式发送数据（分别发送每一个数据块并不做缓存）。 
这样，其实response对象只是缓存消息体的第一个数据块。 

### response.addTrailers(headers)

This method adds HTTP trailing headers (a header but at the end of the
message) to the response.

该方法添加HTTP Trailers 头（在消息的尾部的http头信息）到响应报文中

Trailers will **only** be emitted if chunked encoding is used for the
response; if it is not (e.g., if the request was HTTP/1.0), they will
be silently discarded.

当响应报文使用chunked编码时， Trailers才会触发，否则它们将会被隐式抛弃掉

Note that HTTP requires the `Trailer` header to be sent if you intend to
emit trailers, with a list of the header fields in its value. E.g.,

注意如果你想派发尾部信息版需要添加`Trailer`到HTTP头的参数列表中

    response.writeHead(200, { 'Content-Type': 'text/plain',
                              'Trailer': 'TraceInfo' });
    response.write(fileData);
    response.addTrailers({'Content-MD5': "7895bf4b8828b55ceaf47747b4bca667"});
    response.end();


### response.end([data], [encoding])

This method signals to the server that all of the response headers and body
has been sent; that server should consider this message complete.
The method, `response.end()`, MUST be called on each
response.

这个方法会告诉服务器此响应的所有报文头及报文体已经发出；服务器在此调用后认为这条信息已经发送完毕；
这个方法必须对每个响应调用一次。 

If `data` is specified, it is equivalent to calling `response.write(data, encoding)`
followed by `response.end()`.

如果指定data参数，他就相当于调用了response.write(data, encoding)然后跟着调用了response.end()。 

## http.request(options, callback)

Node maintains several connections per server to make HTTP requests.
This function allows one to transparently issue requests.

Node可以为服务器维护多个连接的HTTP请求，通过这个方法可以向服务器发送请求

Options:

选项：

- `host`: A domain name or IP address of the server to issue the request to.
          请求的域名或者服务器的IP地址
          
- `port`: Port of remote server.
          远端服务器的端口
          
- `method`: A string specifing the HTTP request method. Possible values:
  `'GET'` (default), `'POST'`, `'PUT'`, and `'DELETE'`.
          指定HTTP请求的方法类型类型，可选的值有：`'GET'` (default), 
          `'POST'`, `'PUT'`, and `'DELETE'`。
  
- `path`: Request path. Should include query string and fragments if any.
   E.G. `'/index.html?page=12'`
          请求地址，如果需要可以包含查询字符串片段
   
- `headers`: An object containing request headers.
          一个包含请求头的对象

`http.request()` returns an instance of the `http.ClientRequest`
class. The `ClientRequest` instance is a writable stream. If one needs to
upload a file with a POST request, then write to the `ClientRequest` object.

`http.request()`函数返回`http.ClientRequest`类的一个实例，`ClientRequest`对象
是一个可写入的流，如果你需要用POST方法请求上传一个文件，需要将其写入到`ClientRequest`对象中。

Example:

    var options = {
      host: 'www.google.com',
      port: 80,
      path: '/upload',
      method: 'POST'
    };

    var req = http.request(options, function(res) {
      console.log('STATUS: ' + res.statusCode);
      console.log('HEADERS: ' + JSON.stringify(res.headers));
      res.setEncoding('utf8');
      res.on('data', function (chunk) {
        console.log('BODY: ' + chunk);
      });
    });

    // write data to request body
    req.write('data\n');
    req.write('data\n');
    req.end();

Note that in the example `req.end()` was called. With `http.request()` one
must always call `req.end()` to signify that you're done with the request -
even if there is no data being written to the request body.

注意这个例子中`req.end()`被调用了，无论请求体是否包含数据，每一次调用`http.request()`
最后都需要调用一次`req.end()`表示已经完成了请求

If any error is encountered during the request (be that with DNS resolution,
TCP level errors, or actual HTTP parse errors) an `'error'` event is emitted
on the returned request object.

如果在请求过程中出现了错误（可能是DNS解析、TCP的错误、或者HTTP解析错误），
名为`'error'`的事件将派发到返回的请求对象中

There are a few special headers that should be noted.

如下消息头应当注意： 

* Sending a 'Connection: keep-alive' will notify Node that the connection to
  the server should be persisted until the next request.
  
  发送'Connection: keep-alive'头部将通知Node服务器端的连接将保持到下一个连接到来

* Sending a 'Content-length' header will disable the default chunked encoding.

  发送'Content-length'头部将组织默认的chunked编码

* Sending an 'Expect' header will immediately send the request headers.
  Usually, when sending 'Expect: 100-continue', you should both set a timeout
  and listen for the `continue` event. See RFC2616 Section 8.2.3 for more
  information.
  
  发送'Expect'头部将立即发送请求头部，通常地，当发送'Expect: 100-continue'时，
  你需要监听`continue`事件的同时设置超时，参见RFC2616 8.2.3章节获得更多的信息。

## http.get(options, callback)

Since most requests are GET requests without bodies, Node provides this
convience method. The only difference between this method and `http.request()` is
that it sets the method to GET and calls `req.end()` automatically.

由于很多GET方法的请求不会包含正文体，Node提供了这个方法便于调用，唯一的不同点
是该方法将`http.request()`的方法设置为GET并且自动在最后调用`req.end()`。

Example:

    var options = {
      host: 'www.google.com',
      port: 80,
      path: '/index.html'
    };

    http.get(options, function(res) {
      console.log("Got response: " + res.statusCode);
    }).on('error', function(e) {
      console.log("Got error: " + e.message);
    });


## http.Agent
## http.getAgent(host, port)

`http.request()` uses a special `Agent` for managing multiple connections to
an HTTP server. Normally `Agent` instances should not be exposed to user
code, however in certain situations it's useful to check the status of the
agent. The `http.getAgent()` function allows you to access the agents.

`http.request()`使用一个特别的`Agent`代理来管理服务器上的多个连接，通常`Agent`对象
不应该暴露给用户，但在某些特定的情况下，该对象对用户检测代理的状态非常有用，`http.getAgent()`
函数允许你访问这些代理对象

### Event: 'upgrade'

`function (request, socket, head)`

Emitted each time a server responds to a request with an upgrade. If this event
isn't being listened for, clients receiving an upgrade header will have their
connections closed.

当服务器响应upgrade请求时触发此事件，如果这个消息没有被监听，客户端接收到一个upgrade头的话会导致这个连接被关闭。

See the description of the `upgrade` event for `http.Server` for further details.

可以查看http.Server关于upgrade事件的解释来了解更多内容。 

### Event: 'continue'

`function ()`

Emitted when the server sends a '100 Continue' HTTP response, usually because
the request contained 'Expect: 100-continue'. This is an instruction that
the client should send the request body.

当服务器发送'100 Continue'答复时触发，通常是因为请求包含'Expect: 100-continue',这说明了客户端将要发送请求体

### agent.maxSockets

By default set to 5. Determines how many concurrent sockets the agent can have open.

默认值为5，确定能并发支持多少个打开的套接字

### agent.sockets

An array of sockets currently inuse by the Agent. Do not modify.

当前代理管理的套接字数组，无法更改，只读

### agent.queue

A queue of requests waiting to be sent to sockets.

待发送到套接字的请求队列

## http.ClientRequest

This object is created internally and returned from `http.request()`.  It
represents an _in-progress_ request whose header has already been queued.  The 
header is still mutable using the `setHeader(name, value)`, `getHeader(name)`,
`removeHeader(name)` API.  The actual header will be sent along with the first
data chunk or when closing the connection.

这个对象由内部创建，通过`http.request()`返回该对象，它表示一个正在进行中且
头部信息已经安排好了的请求，这时候通过`setHeader(name, value)`, `getHeader(name)`,
`removeHeader(name)`这些API是无法改变头部信息的，实际的头部信息将随着chunk的第一
部分或者关闭连接时发送出去

To get the response, add a listener for `'response'` to the request object.
`'response'` will be emitted from the request object when the response
headers have been received.  The `'response'` event is executed with one
argument which is an instance of `http.ClientResponse`.

为了获得响应，为请求对象增加一个对响应的监听器。

During the `'response'` event, one can add listeners to the
response object; particularly to listen for the `'data'` event. Note that
the `'response'` event is called before any part of the response body is received,
so there is no need to worry about racing to catch the first part of the
body. As long as a listener for `'data'` is added during the `'response'`
event, the entire body will be caught.

在`'response'`事件中，可以给响应对象添加监听器，特别是监听`'data'`事件，
注意`'response'`事件在正文体接收之前就已经被调用，所以不需要担心捕获不到正文体的第一部分，
一旦在`'response'`事件中添加了对`'data'`的监听器，那么整个消息体将被捕获

    // Good
    request.on('response', function (response) {
      response.on('data', function (chunk) {
        console.log('BODY: ' + chunk);
      });
    });

    // Bad - misses all or part of the body
    request.on('response', function (response) {
      setTimeout(function () {
        response.on('data', function (chunk) {
          console.log('BODY: ' + chunk);
        });
      }, 10);
    });

This is a `Writable Stream`.

这是一个`Writable Stream`

This is an `EventEmitter` with the following events:

此方法会触发以下事件

### Event 'response'

`function (response) { }`

Emitted when a response is received to this request. This event is emitted only once. The
`response` argument will be an instance of `http.ClientResponse`.

当请求的响应到达时触发，该事件仅触发一次，`response`参数是`http.ClientResponse`的一个实例

### request.write(chunk, encoding='utf8')

Sends a chunk of the body.  By calling this method
many times, the user can stream a request body to a
server--in that case it is suggested to use the
`['Transfer-Encoding', 'chunked']` header line when
creating the request.

发送body中的一块。用户可以通过多次调用这个方法将请求数据包通过流的方式发送到服务器。
在这个时候我们建议使用在建立请求的时候把['Transfer-Encoding', 'chunked']放在请求头里。 

The `chunk` argument should be an array of integers
or a string.

参数'chunk'应当是一个数字索引的数组或字符串。 

The `encoding` argument is optional and only
applies when `chunk` is a string.

参数'encoding'是可选的，仅在chunk为字符串的时使用。 


### request.end([data], [encoding])

Finishes sending the request. If any parts of the body are
unsent, it will flush them to the stream. If the request is
chunked, this will send the terminating `'0\r\n\r\n'`.

完成本次请求的发送。如果消息体中的任何一个部分没有来得及发送，request.end将把他们全部刷新到流中。
如果本次请求是分块的，这个函数将发出结束字符'0\r\n\r\n'。 

If `data` is specified, it is equivalent to calling `request.write(data, encoding)`
followed by `request.end()`.

如果使用参数data，就等于在调用request.write(data, encoding)之后紧接着调用request.end()。 

### request.abort()

Aborts a request.  (New since v0.3.8.)

阻止一个请求

## http.ClientResponse

This object is created when making a request with `http.request()`. It is
passed to the `'response'` event of the request object.

这个对象在使用http.Client发起请求时被创建，它会以参数的形式传递给request对象'response'事件的响应函数。 

The response implements the `Readable Stream` interface.

'response'实现了可读流的接口。

### Event: 'data'

`function (chunk) {}`

Emitted when a piece of the message body is received.

当接收到消息体一部分的时候触发。 

### Event: 'end'

`function () {}`

Emitted exactly once for each message. No arguments. After
emitted no other events will be emitted on the response.

对每次消息请求只触发一次，该事件触发后将不会有任何对象在响应中触发

### response.statusCode

The 3-digit HTTP response status code. E.G. `404`.

响应头部的状态码

### response.httpVersion

The HTTP version of the connected-to server. Probably either
`'1.1'` or `'1.0'`.
Also `response.httpVersionMajor` is the first integer and
`response.httpVersionMinor` is the second.

连接至服务器端的HTTP版本，可能的值为`'1.1'` or `'1.0'`，你也
可以使用`response.httpVersionMajor`获得版本号第一位，使用`response.httpVersionMinor`
获得版本号第二位

### response.headers

The response headers object.

响应头部对象

### response.trailers

The response trailers object. Only populated after the 'end' event.

响应尾部对象，在'end'事件发生后生成该对象

### response.setEncoding(encoding=null)

Set the encoding for the response body. Either `'utf8'`, `'ascii'`, or `'base64'`.
Defaults to `null`, which means that the `'data'` event will emit a `Buffer` object..

设置响应体的编码，可以是`'utf8'`, `'ascii'`, 或者 `'base64'`，默认值为`null`，
也就是说`'data'`事件将发送缓冲区对象

### response.pause()

Pauses response from emitting events.  Useful to throttle back a download.

停止应当派发事件，对中断下载非常有用。

### response.resume()

Resumes a paused response.

恢复一个暂停的响应

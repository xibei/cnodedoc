## HTTP HTTP模块

To use the HTTP server and client one must `require('http')`.

如果要使用HTTP的服务器以及客户端，需使用`require('http')`加载HTTP模块。 

The HTTP interfaces in Node are designed to support many features
of the protocol which have been traditionally difficult to use.
In particular, large, possibly chunk-encoded, messages. The interface is
careful to never buffer entire requests or responses--the
user is able to stream data.

Node中的HTTP接口在设计时就考虑到了要支持HTTP协议的很多特性，并且使用简单。特别是可以处理那些内容庞大，有可能是块编码的消息。该接口被设计为从不缓冲整个请求或相应，这样用户就可以以流的方式处理数据。 

HTTP message headers are represented by an object like this:

HTTP头信息以如下对象形式表示：

    { 'content-length': '123',
      'content-type': 'text/plain',
      'connection': 'keep-alive',
      'accept': '*/*' }

Keys are lowercased. Values are not modified.

所有键名被转为小写，而值不会被修改。

In order to support the full spectrum of possible HTTP applications, Node's
HTTP API is very low-level. It deals with stream handling and message
parsing only. It parses a message into headers and body but it does not
parse the actual headers or the body.

为了支持尽可能多的HTTP应用，Node提供非常底层的HTTP API。它只处理流相关的操作以及进行信息解析。API将信息解析为头部和正文，但并不解析实际的头部和正文内的具体内容。

## http.Server

This is an `EventEmitter` with the following events:

这是一个带有如下事件的`EventEmitter`事件触发器：

### Event: 'request' 事件：'request'

`function (request, response) { }`

 `request` is an instance of `http.ServerRequest` and `response` is
 an instance of `http.ServerResponse`
 
 `request`是`http.ServerRequest`的一个实例，而`response`是`http.ServerResponse`的一个实例。
 
### Event: 'connection' 事件：'connection'

`function (stream) { }`

 When a new TCP stream is established. `stream` is an object of type
 `net.Stream`. Usually users will not want to access this event. The
 `stream` can also be accessed at `request.connection`.
 
 当一个新的TCP流建立后触发此事件。`stream`是一个`net.Stream`类型的对象，通常用户不会使用这个事件。参数`stream`也可以在`request.connection`中获得。

### Event: 'close' 事件：'close'

`function (errno) { }`

 Emitted when the server closes.
 
 当服务器关闭的时候触发此事件。 

### Event: 'request' 事件：'request'

`function (request, response) {}`

Emitted each time there is request. Note that there may be multiple requests
per connection (in the case of keep-alive connections).

每个请求发生的时候均会被触发。请注意每个连接可能会有多个请求（在keep-alive连接情况下）。

### Event: 'checkContinue' 事件：'checkContinue'

`function (request, response) {}`

Emitted each time a request with an http Expect: 100-continue is received.
If this event isn't listened for, the server will automatically respond
with a 100 Continue as appropriate.

每当带有Exception: 100-continue头的请求被接收到时触发此事件。如果该事件未被监听，服务器会视情况自动的使用100 Continue应答。

Handling this event involves calling `response.writeContinue` if the client
should continue to send the request body, or generating an appropriate HTTP
response (e.g., 400 Bad Request) if the client should not continue to send the
request body.

该事件的处理涉及两种情况，如果客户端应当继续发送请求正文，那么需要调用`response.writeContinue`，而如果客户端不应该继续发送请求正文，那么应该产生一个适当的HTTP回应（如400错误请求）。

Note that when this event is emitted and handled, the `request` event will
not be emitted.

注意如果该事件被触发并处理的话，那么将不再触发`request`事件。

### Event: 'upgrade' 事件：'upgrade'

`function (request, socket, head)`

Emitted each time a client requests a http upgrade. If this event isn't
listened for, then clients requesting an upgrade will have their connections
closed.

每当一个客户端请求http upgrade时触发此消息。如果这个事件没有监听，那么请求upgrade的客户端的连接将被关闭。 

* `request` is the arguments for the http request, as it is in the request event.
  `request`代表一个http请求的相关参数，和它在request事件中的意思相同。

* `socket` is the network socket between the server and client.
  `socket`是在服务器与客户端之间连接使用的网络套接字。 

* `head` is an instance of Buffer, the first packet of the upgraded stream, this may be empty.
  `head`是一个缓冲器实例，是upgraded流的第一个包，这个缓冲器可以是空的。 

After this event is emitted, the request's socket will not have a `data`
event listener, meaning you will need to bind to it in order to handle data
sent to the server on that socket.

当此事件被触发后，该请求所使用的套接字将不会有一个`data`事件监听器。这意味着你如果需要处理通过这个套接字发送到服务器端的数据则需要自己绑定`data`事件监听器。 

### Event: 'clientError' 事件：'clientError'

`function (exception) {}`

If a client connection emits an 'error' event - it will forwarded here.

当客户端连接出现错误时会触发'error'事件。

### http.createServer(requestListener)

Returns a new web server object.

返回一个新的web server对象。 

The `requestListener` is a function which is automatically
added to the `'request'` event.

requestListener监听器会自动添加到`'request'`事件中。

### server.listen(port, [hostname], [callback])

Begin accepting connections on the specified port and hostname.  If the
hostname is omitted, the server will accept connections directed to any
IPv4 address (`INADDR_ANY`).

在指定端口和主机名上接受连接。如果hostname没有指定，服务器将直接在此机器的所有IPV4地址上接受连接(`INADDR_ANY`)。 

To listen to a unix socket, supply a filename instead of port and hostname.

如果要在UNIX套接字上监听的话，则需要提供一个文件名来替换端口和主机名。 

This function is asynchronous. The last parameter `callback` will be called
when the server has been bound to the port.

这个方法是一个异步的方法，当服务器已经在此端口上完成绑定后讲调用`callback`回调函数。 

### server.listen(path, [callback])

Start a UNIX socket server listening for connections on the given `path`.

建立一个UNIX套接字服务器并在指定`path`路径上监听。 

This function is asynchronous. The last parameter `callback` will be called
when the server has been bound.

这个方法是一个异步的方法，当服务器完成绑定后将调用`callback`回调函数。

### server.close()

Stops the server from accepting new connections.

使此服务器停止接受任何新连接。

## http.ServerRequest

This object is created internally by a HTTP server -- not by
the user -- and passed as the first argument to a `'request'` listener.

这个对象通常由HTTP服务器（而非用户）自动建立，并作为第一个参数传给`'request'`监听器。

This is an `EventEmitter` with the following events:

这是一个带有如下事件的`EventEmitter`事件触发器： 

### Event: 'data' 事件：'data'

`function (chunk) { }`

Emitted when a piece of the message body is received.

当接收到信息正文中的一部分时候会触发此事件。 

Example: A chunk of the body is given as the single
argument. The transfer-encoding has been decoded.  The
body chunk is a string.  The body encoding is set with
`request.setBodyEncoding()`.

例如：正文的数据块将作为唯一的参数传递给回调函数。此时传输编码已被解码。正文数据块是一个字符串，正文的编码由`request.setBodyEncoding()`方法设定。 

### Event: 'end' 事件：'end'

`function () { }`

Emitted exactly once for each message. No arguments.  After
emitted no other events will be emitted on the request.

每次完全接收完信息后都会触发一次，不接受任何参数。当这个事件被触发后，将不会再触发其他事件。 

### request.method

The request method as a string. Read only. Example:
`'GET'`, `'DELETE'`.

表示请求方式的只读字符串。例如`'GET'`，`'DELETE'`。 

### request.url

Request URL string. This contains only the URL that is
present in the actual HTTP request. If the request is:

代表所请求URL的字符串。他仅包括实际的HTTP请求中的URL地址。如果这个请求是： 

    GET /status?name=ryan HTTP/1.1\r\n
    Accept: text/plain\r\n
    \r\n

Then `request.url` will be:

则`request.url`应当是：

    '/status?name=ryan'

If you would like to parse the URL into its parts, you can use
`require('url').parse(request.url)`.  Example:

如果你想要解析这个URL中的各个部分，可以使用`require('url').parse(request.url)`。例如： 

    node> require('url').parse('/status?name=ryan')
    { href: '/status?name=ryan',
      search: '?name=ryan',
      query: 'name=ryan',
      pathname: '/status' }

If you would like to extract the params from the query string,
you can use the `require('querystring').parse` function, or pass
`true` as the second argument to `require('url').parse`.  Example:

如果你想从查询字符串中提取所有参数，你可以使用`require('querystring').parse`方法，或者传一个`true`作为第二个参数给`require('url').parse`方法。例如：

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

只读，HTTP尾部（如果存在的话），只有在'end'事件被触发后该值才会被填充。

### request.httpVersion

The HTTP protocol version as a string. Read only. Examples:
`'1.1'`, `'1.0'`.
Also `request.httpVersionMajor` is the first integer and
`request.httpVersionMinor` is the second.

只读的，以字符串形式表示HTTP协议版本。例如`'1.1'`，`'1.0'`。`request.httpVersionMajor`对应版本号的第一个数字，`request.httpVersionMinor`则对应第二个数字。 

### request.setEncoding(encoding=null)

Set the encoding for the request body. Either `'utf8'` or `'binary'`. Defaults
to `null`, which means that the `'data'` event will emit a `Buffer` object..

设置此请求正文的字符编码，`'utf8'`或者`'binary'`。缺省值是`null`，这表示`'data'`事件的参数将会是一个缓冲器对象。 

### request.pause()

Pauses request from emitting events.  Useful to throttle back an upload.

暂停此请求的事件触发。对于控制上传非常有用。 

### request.resume()

Resumes a paused request.

恢复一个暂停的请求。

### request.connection

The `net.Stream` object associated with the connection.

与当前连接相关联的`net.Stream`对象。

With HTTPS support, use request.connection.verifyPeer() and
request.connection.getPeerCertificate() to obtain the client's
authentication details.

对于使用HTTPS的连接，可使用request.connection.verifyPeer()和request.connection.getPeerCertificate()来获得客户端的认证详情。 

## http.ServerResponse

This object is created internally by a HTTP server--not by the user. It is
passed as the second parameter to the `'request'` event. It is a `Writable Stream`.

这个对象由HTTP服务器（而非用户）自动建立。它作为`'request'`事件的第二个参数，这是一个`Writable Stream`可写流。

### response.writeContinue()

Sends a HTTP/1.1 100 Continue message to the client, indicating that
the request body should be sent. See the the `checkContinue` event on
`Server`.

发送HTTP/1.1 100 Continue消息给客户端，通知客户端可以发送请求的正文。参见服务器`Server`中的`checkContinue`事件。

### response.writeHead(statusCode, [reasonPhrase], [headers])

Sends a response header to the request. The status code is a 3-digit HTTP
status code, like `404`. The last argument, `headers`, are the response headers.
Optionally one can give a human-readable `reasonPhrase` as the second
argument.

这个方法用来发送一个响应头，statusCode是一个由3位数字所构成的HTTP状态码，比如`404`之类。最后一个参数`headers`是响应头具体内容。也可以使用一个方便人们直观理解的`reasonPhrase`作为第二个参数。 

Example:

    var body = 'hello world';
    response.writeHead(200, {
      'Content-Length': body.length,
      'Content-Type': 'text/plain' });

This method must only be called once on a message and it must
be called before `response.end()` is called.

在一次请求响应中此方法只能调用一次，并且必须在调用`response.end()`之前调用。 

If you call `response.write()` or `response.end()` before calling this, the
implicit/mutable headers will be calculated and call this function for you.

如果你在`response.write()`或者`response.end()`之后调用此方法，响应头的内容将是不确定而且不可知的。

### response.statusCode

When using implicit headers (not calling `response.writeHead()` explicitly), this property
controls the status code that will be send to the client when the headers get
flushed.

当隐式的发送响应头信息（没有明确调用`response.writeHead()`）时，使用此属性将设置返回给客户端的状态码。状态吗将在响应头信息发送时一起被发送。

Example:

例如：

    response.statusCode = 404;

### response.setHeader(name, value)

Sets a single header value for implicit headers.  If this header already exists
in the to-be-sent headers, it's value will be replaced.  Use an array of strings
here if you need to send multiple headers with the same name.

在隐式的响应头基础上设置单个头信息。如果存在同名的待发送头信息，那么该头信息的值将被替换。如果你想发送相同名字的多个头部信息，可以使用字符串数组的形式设置。

Example:

例如：

    response.setHeader("Content-Type", "text/html");

或者

    response.setHeader("Set-Cookie", ["type=ninja", "language=javascript"]);


### response.getHeader(name)

Reads out a header that's already been queued but not sent to the client.  Note
that the name is case insensitive.  This can only be called before headers get
implicitly flushed.

读取已经排列好但尚未发送给客户端的头部信息，注意参数名不区分大小写。此方法必须在响应头信息隐式发送之前调用。

Example:

例如：

    var contentType = response.getHeader('content-type');

### response.removeHeader(name)

Removes a header that's queued for implicit sending.

移除等待隐式发送的头部信息。

Example:

例如：

    response.removeHeader("Content-Encoding");


### response.write(chunk, encoding='utf8')

If this method is called and `response.writeHead()` has not been called, it will
switch to implicit header mode and flush the implicit headers.

如果在`response.writeHead()`调用之前调用该函数，将会切换到隐式发送响应头信息的模式并发送隐式的头部信息。 

This sends a chunk of the response body. This method may
be called multiple times to provide successive parts of the body.

它负责发送响应正文中的一部分数据，可以多次调用此方法以发送正文中多个连续的部分。

`chunk` can be a string or a buffer. If `chunk` is a string,
the second parameter specifies how to encode it into a byte stream.
By default the `encoding` is `'utf8'`.

`chunk`可以是一个字符串或者一个缓冲器。如果`chunk`是一个字符串，则第二个参数指定用何种编码方式将字符串编码为字节流。缺省情况下，`encoding`为`'utf8'`。 

**Note**: This is the raw HTTP body and has nothing to do with
higher-level multi-part body encodings that may be used.

**注意**：这是一个原始格式HTTP正文，和高层协议中的多段正文编码格式无关。 

The first time `response.write()` is called, it will send the buffered
header information and the first body to the client. The second time
`response.write()` is called, Node assumes you're going to be streaming
data, and sends that separately. That is, the response is buffered up to the
first chunk of body.

第一次调用`response.write()`时，此方法会将已经缓冲的消息头和第一块正文发送给客户。 当第二次调用`response.write()`的时候，Node将假定你想要逐次发送流数据。换句话说，响应被缓冲直到正文的第一块被发送。 

### response.addTrailers(headers)

This method adds HTTP trailing headers (a header but at the end of the
message) to the response.

该方法在响应中添加HTTP尾部头信息（在消息尾部的头信息）。

Trailers will **only** be emitted if chunked encoding is used for the
response; if it is not (e.g., if the request was HTTP/1.0), they will
be silently discarded.

**仅当**响应报文使用chunked编码时，尾部信息才会发送；否则（例如请求的协议版本为HTTP/1.0）它们会被抛弃而没有提示。

Note that HTTP requires the `Trailer` header to be sent if you intend to
emit trailers, with a list of the header fields in its value. E.g.,

注意如果你想发送尾部信息，则需要在HTTP头中添加`Trailer`。

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

这个方法通知服务器所有的响应头和响应正文都已经发出；服务器在此调用后认为这条信息已经发送完毕。在每个响应上都必须调用`response.end()`方法。 

If `data` is specified, it is equivalent to calling `response.write(data, encoding)`
followed by `response.end()`.

如果指定了`data`参数，就相当先调用`response.write(data, encoding)`再调用`response.end()`。

## http.request(options, callback)

Node maintains several connections per server to make HTTP requests.
This function allows one to transparently issue requests.

Node为一个目标服务器维护多个连接用于HTTP请求。通过这个方法可以向服务器发送请求。

Options:

选项：

- `host`: A domain name or IP address of the server to issue the request to.
  `host`: 请求的服务器域名或者IP地址。
          
- `port`: Port of remote server.
  `port`: 远端服务器的端口。
          
- `method`: A string specifying the HTTP request method. Possible values:
  `'GET'` (default), `'POST'`, `'PUT'`, and `'DELETE'`.
  `method`: 指定HTTP请求的方法类型，可选的值有：`'GET'`（默认），`'POST'`，`'PUT'`，以及`'DELETE'`。
  
- `path`: Request path. Should include query string and fragments if any.
   E.G. `'/index.html?page=12'`
  `path`: 请求地址，可包含查询字符串以及可能存在的锚点。例如`'/index.html?page=12'`。
   
- `headers`: An object containing request headers.
  `headers`: 一个包含请求头的对象。

`http.request()` returns an instance of the `http.ClientRequest`
class. The `ClientRequest` instance is a writable stream. If one needs to
upload a file with a POST request, then write to the `ClientRequest` object.

`http.request()`函数返回`http.ClientRequest`类的一个实例。`ClientRequest`对象是一个可写流，如果你需要用POST方法上传一个文件，可将其写入到`ClientRequest`对象中。

Example:

例子：

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

注意这个例子中`req.end()`被调用了。无论请求正文是否包含数据，每一次调用`http.request()`最后都需要调用一次`req.end()`表示已经完成了请求。

If any error is encountered during the request (be that with DNS resolution,
TCP level errors, or actual HTTP parse errors) an `'error'` event is emitted
on the returned request object.

如果在请求过程中出现了错误（可能是DNS解析、TCP的错误、或者HTTP解析错误），返回的请求对象上的`'error'`的事件将被触发。

There are a few special headers that should be noted.

如下特别的消息头应当注意：

* Sending a 'Connection: keep-alive' will notify Node that the connection to
  the server should be persisted until the next request.
  
  发送'Connection: keep-alive'头部将通知Node此连接将保持到下一此请求。

* Sending a 'Content-length' header will disable the default chunked encoding.

  发送'Content-length'头将使默认的分块编码无效。

* Sending an 'Expect' header will immediately send the request headers.
  Usually, when sending 'Expect: 100-continue', you should both set a timeout
  and listen for the `continue` event. See RFC2616 Section 8.2.3 for more
  information.
  
  发送'Expect'头部将引起请求头部立即被发送。通常情况，当发送'Expect: 100-continue'时，你需要监听`continue`事件的同时设置超时。参见RFC2616 8.2.3章节以获得更多的信息。

## http.get(options, callback)

Since most requests are GET requests without bodies, Node provides this
convenience method. The only difference between this method and `http.request()` is
that it sets the method to GET and calls `req.end()` automatically.

由于大部分请求是不包含正文的GET请求，Node提供了这个方便的方法。与`http.request()`唯一的区别是此方法将请求方式设置为GET，并且自动调用`req.end()`。

Example:

例子：

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

`http.request()`使用一个特别的`Agent`代理来管理到一个服务器的多个连接，通常`Agent`对象不应该暴露给用户。但在某些特定的情况下，检测代理的状态是非常有用的。`http.getAgent()`函数允许你访问代理对象。

### Event: 'upgrade' 事件：'upgrade'

`function (request, socket, head)`

Emitted each time a server responds to a request with an upgrade. If this event
isn't being listened for, clients receiving an upgrade header will have their
connections closed.

当服务器响应upgrade请求时触发此事件。如果这个事件没有被监听，客户端接收到upgrade头会导致连接被关闭。

See the description of the `upgrade` event for `http.Server` for further details.

可以查看http.Server关于upgrade事件的解释来了解更多内容。 

### Event: 'continue' 事件：'continue'

`function ()`

Emitted when the server sends a '100 Continue' HTTP response, usually because
the request contained 'Expect: 100-continue'. This is an instruction that
the client should send the request body.

当服务器发送'100 Continue'答复时触发此事件，这通常是因为请求头信息中包含'Expect: 100-continue'。此事件指示客户端可是开始发送请求正文了。

### agent.maxSockets

By default set to 5. Determines how many concurrent sockets the agent can have open.

默认值为5，指定代理能同时并发打开的套接字数量。

### agent.sockets

An array of sockets currently in use by the Agent. Do not modify.

当前代理使用的套接字数组，不能更改。

### agent.queue

A queue of requests waiting to be sent to sockets.

待发送到套接字的请求队列。

## http.ClientRequest

This object is created internally and returned from `http.request()`.  It
represents an _in-progress_ request whose header has already been queued.  The 
header is still mutable using the `setHeader(name, value)`, `getHeader(name)`,
`removeHeader(name)` API.  The actual header will be sent along with the first
data chunk or when closing the connection.

这个对象是在调用`http.request()`时产生并返回的。它表示一个_正在进行_中且头部信息已经排列好了的请求。这时候通过`setHeader(name, value)`，`getHeader(name)`，`removeHeader(name)`这些API还可以改变头部信息，实际的头部信息将随着第一块数据发送，或者在关闭连接时发送出去。

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

在`'response'`事件中，可以给响应对象添加监听器，特别是监听`'data'`事件，注意`'response'`事件在正文接收之前就已经被调用，所以不需要担心捕获不到正文的第一部分，一旦在`'response'`事件中添加了对`'data'`的监听器，那么整个正文将被捕获。

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

这是一个`Writable Stream`可写流。

This is an `EventEmitter` with the following events:

这是一个包含下述事件的`EventEmitter`事件触发器：

### Event 'response' 事件：'response'

`function (response) { }`

Emitted when a response is received to this request. This event is emitted only once. The
`response` argument will be an instance of `http.ClientResponse`.

当请求的响应到达时触发，该事件仅触发一次。`response`参数是`http.ClientResponse`的一个实例。

### request.write(chunk, encoding='utf8')

Sends a chunk of the body.  By calling this method
many times, the user can stream a request body to a
server--in that case it is suggested to use the
`['Transfer-Encoding', 'chunked']` header line when
creating the request.

发送正文中的一块。用户可以通过多次调用这个方法将请求正文以流的方式发送到服务器。此种情况建议在建立请求时使用`['Transfer-Encoding', 'chunked']`请求头。 

The `chunk` argument should be an array of integers
or a string.

参数`chunk`应当是一个整数数组或字符串。 

The `encoding` argument is optional and only
applies when `chunk` is a string.

参数`encoding`是可选的，仅在`chunk`为字符串时可用。 


### request.end([data], [encoding])

Finishes sending the request. If any parts of the body are
unsent, it will flush them to the stream. If the request is
chunked, this will send the terminating `'0\r\n\r\n'`.

完成本次请求的发送。如果正文中的任何一个部分没有来得及发送，将把他们全部刷新到流中。如果本次请求是分块的，这个函数将发出结束字符`'0\r\n\r\n'`。 

If `data` is specified, it is equivalent to calling `request.write(data, encoding)`
followed by `request.end()`.

如果使用参数data，就等于在调用request.write(data, encoding)之后紧接着调用request.end()。 

### request.abort()

Aborts a request.  (New since v0.3.8.)

阻止一个请求。（v0.3.8中新增的方法。）

## http.ClientResponse

This object is created when making a request with `http.request()`. It is
passed to the `'response'` event of the request object.

这个对象在使用`http.request()`发起请求时被创建，它会以参数的形式传递给request对象的`'response'`事件。 

The response implements the `Readable Stream` interface.

'response'实现了可读流的接口。

### Event: 'data' 事件：'data'

`function (chunk) {}`

Emitted when a piece of the message body is received.

当接收到消息正文一部分的时候触发。 

### Event: 'end' 事件：'end'

`function () {}`

Emitted exactly once for each message. No arguments. After
emitted no other events will be emitted on the response.

对每次消息请求只触发一次，该事件被触发后将不会再有任何事件在响应中被触发。

### response.statusCode

The 3-digit HTTP response status code. E.G. `404`.

3个数字组成的HTTP响应状态吗。例如`404`。

### response.httpVersion

The HTTP version of the connected-to server. Probably either
`'1.1'` or `'1.0'`.
Also `response.httpVersionMajor` is the first integer and
`response.httpVersionMinor` is the second.

连接至服务器端的HTTP版本，可能的值为`'1.1'` or `'1.0'`，你也可以使用`response.httpVersionMajor`获得版本号第一位，使用`response.httpVersionMinor`获得版本号第二位。

### response.headers

The response headers object.

响应头部对象。

### response.trailers

The response trailers object. Only populated after the 'end' event.

响应尾部对象，在'end'事件发生后填充该对象。

### response.setEncoding(encoding=null)

Set the encoding for the response body. Either `'utf8'`, `'ascii'`, or `'base64'`.
Defaults to `null`, which means that the `'data'` event will emit a `Buffer` object..

设置响应正文的编码，可以是`'utf8'`，`'ascii'`，或者`'base64'`。默认值为`null`，此种情况下`'data'`事件将发送缓冲器对象。

### response.pause()

Pauses response from emitting events.  Useful to throttle back a download.

暂停响应的事件激发，对控制下载流量非常有用。

### response.resume()

Resumes a paused response.

恢复一个已经暂停的响应。

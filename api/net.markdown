## net 网络模块

The `net` module provides you with an asynchronous network wrapper. It contains
methods for creating both servers and clients (called streams). You can include
this module with `require("net");`

`net`模块为你提供了一种异步网络包装器，它包含创建服务器和客户端（称为streams）所需的方法，您可以通过调用`require("net")`来使用此模块。

### net.createServer([options], [connectionListener])

Creates a new TCP server. The `connectionListener` argument is
automatically set as a listener for the `'connection'` event.

创建一个新的TCP服务器，参数`connectionListener`被自动设置为connection事件的监听器。

`options` is an object with the following defaults:

`options`参数为一个对象，默认值如下：
    { allowHalfOpen: false
    }

If `allowHalfOpen` is `true`, then the socket won't automatically send FIN
packet when the other end of the socket sends a FIN packet. The socket becomes
non-readable, but still writable. You should call the end() method explicitly.
See `'end'` event for more information.

如果`allowHalfOpen`参数为`true`，则当客户端socket发送FIN包时，服务器端socket不会自动发送FIN包。此情况下服务器端socket将变为不可读状态，但仍然可写。你需要明确的调用end()方法来关闭连接。更多内容请参照`'end'`事件。

### net.createConnection(arguments...)

Construct a new socket object and opens a socket to the given location. When
the socket is established the `'connect'` event will be emitted.

创建一个新的socket对象，并建立到指定地址的socket连接。当socket建立后，`'connect'`事件将被触发。

The arguments for this method change the type of connection:

不同的参数决定了连接的类型：

* `net.createConnection(port, [host])`

  Creates a TCP connection to `port` on `host`. If `host` is omitted, `localhost`
  will be assumed.

  创建一个到主机`host`的`port`端口的TCP连接，如果略了`host`参数，默认连接到`localhost`。

* `net.createConnection(path)`

  Creates unix socket connection to `path`

  创建连接到`path`路径的unix socket。

---

### net.Server

This class is used to create a TCP or UNIX server.

这个类用于创建一个TCP或UNIX服务器。

Here is an example of a echo server which listens for connections
on port 8124:

下面的例子创建了一个在8124端口监听的`echo`服务器。

    var net = require('net');
    var server = net.createServer(function (c) {
      c.write('hello\r\n');
      c.pipe(c);
    });
    server.listen(8124, 'localhost');

Test this by using `telnet`:

使用`telnet`测试该服务器。

    telnet localhost 8124

To listen on the socket `/tmp/echo.sock` the last line would just be
changed to

如要监听socket `/tmp/echo.sock`，最后一行代码需要修改成：

    server.listen('/tmp/echo.sock');

Use `nc` to connect to a UNIX domain socket server:

使用`nc`命令连接到一个UNIX域socket服务器：

    nc -U /tmp/echo.sock

`net.Server` is an `EventEmitter` with the following events:

`net.Server`是下列事件的 `EventEmitter`（事件触发器）:

#### server.listen(port, [host], [callback])

Begin accepting connections on the specified `port` and `host`.  If the
`host` is omitted, the server will accept connections directed to any
IPv4 address (`INADDR_ANY`).

开始接收特定主机`host`的`port`端口的连接，如果省略了`host`参数，服务器将接收任何指向IPV4地址的连接。

This function is asynchronous. The last parameter `callback` will be called
when the server has been bound.

此函数是异步的，在服务器被绑定时，最后一个参数`callback`回调函数将被调用。

One issue some users run into is getting `EADDRINUSE` errors. Meaning
another server is already running on the requested port. One way of handling this
would be to wait a second and the try again. This can be done with

一些用户可能会遇到`EADDRINUSE` 错误，该错误消息的意思是已经有另一个服务运行在请求的端口上，一个解决方法就是等一会再试一下，就像下面的代码这样：

    server.on('error', function (e) {
      if (e.code == 'EADDRINUSE') {
        console.log('Address in use, retrying...');
        setTimeout(function () {
          server.close();
          server.listen(PORT, HOST);
        }, 1000);
      }
    });

(Note: All sockets in Node are set SO_REUSEADDR already)

（注意：Node中所有的socket都已经设置成SO_REUSEADDR端口重用模式）

#### server.listen(path, [callback])

Start a UNIX socket server listening for connections on the given `path`.

启动一个UNIX socket服务，监听指定的`path`路径上的连接。

This function is asynchronous. The last parameter `callback` will be called
when the server has been bound.

此函数是异步的，在服务器被绑定时，最后一个参数`callback`回调函数将被调用。

#### server.listenFD(fd)

Start a server listening for connections on the given file descriptor.

启动一个服务，监听指定的文件描述符上的连接。

This file descriptor must have already had the `bind(2)` and `listen(2)` system
calls invoked on it.

此文件描述符上必须已经执行了 `bind(2)` 和`listen(2)` 系统调用。

#### server.close()

Stops the server from accepting new connections. This function is
asynchronous, the server is finally closed when the server emits a `'close'`
event.

关闭服务，停止接收新的连接。该函数是异步的，当服务发出`'close'`事件时该服务器被最终关闭。


#### server.address()

Returns the bound address of the server as seen by the operating system.
Useful to find which port was assigned when giving getting an OS-assigned address

返回绑定到操作系统的服务器地址。如果绑定地址是由操作系统自动分配的，可用此方法查看具体的端口号。

Example:

    var server = net.createServer(function (socket) {
      socket.end("goodbye\n");
    });

    // grab a random port.
    server.listen(function() {
      address = server.address();
      console.log("opened server on %j", address);
    });


#### server.maxConnections

Set this property to reject connections when the server's connection count gets high.

设置该属性的值，以便当服务器达到最大连接数时不再接受新的连接。

#### server.connections

The number of concurrent connections on the server.

服务器的并发连接数。

#### Event: 'connection' 事件：'connection'

`function (socket) {}`

Emitted when a new connection is made. `socket` is an instance of
`net.Socket`.

当一个新的连接建立时触发。`socket` 是`net.Socket`的一个实例。

#### Event: 'close'

`function () {}`

Emitted when the server closes.

当服务器关闭时触发。

---

### net.Socket

This object is an abstraction of of a TCP or UNIX socket.  `net.Socket`
instances implement a duplex Stream interface.  They can be created by the
user and used as a client (with `connect()`) or they can be created by Node
and passed to the user through the `'connection'` event of a server.

这是TCP或UNIX socket的抽象对象。`net.Socket`实例实现了一个全双工的流接口。此实例可以是由用户建立用作客户端（使用`connect()`方法），也可能由Node建立并通过服务器的`'connection'`事件传给用户。

`net.Socket` instances are EventEmitters with the following events:

`net.Socket` 的实例是下列事件的事件触发器:

#### new net.Socket([options])

Construct a new socket object.

构造一个新的socket对象。

`options` is an object with the following defaults:

`options`参数是一个对象，默认值如下：

    { fd: null
      type: null
      allowHalfOpen: false
    }

`fd` allows you to specify the existing file descriptor of socket. `type`
specified underlying protocol. It can be `'tcp4'`, `'tcp6'`, or `'unix'`.
About `allowHalfOpen`, refer to `createServer()` and `'end'` event.

`fd`参数允许你指定一个已经存在的socket的文件描述符。`type`参数用于指定底层协议，可选值包括`'tcp4'`，`'tcp6'`或`'unix'`。关于`allowHalfOpen`，可参考`createServer()`和`'end'`事件。

#### socket.connect(port, [host], [callback])
#### socket.connect(path, [callback])

Opens the connection for a given socket. If `port` and `host` are given,
then the socket will be opened as a TCP socket, if `host` is omitted,
`localhost` will be assumed. If a `path` is given, the socket will be
opened as a unix socket to that path.

打开一下指定socket的连接。如果给出了`port` 和 `host`，将作为一个TCP socket打开，如果省略了`host`，将默认连接到`localhost`。如果指定了`path`，该socket将作为一个UNIX socket打开，并连接到`path`路径。


Normally this method is not needed, as `net.createConnection` opens the
socket. Use this only if you are implementing a custom Socket or if a
Socket is closed and you want to reuse it to connect to another server.

通常情况下该方法并不需要，使用 `net.createConnection` 就可以打开socket。只有在你实现一个自定义的socket，或者你想重用一个已经关闭的socket连接到另一个服务器。

This function is asynchronous. When the `'connect'` event is emitted the
socket is established. If there is a problem connecting, the `'connect'`
event will not be emitted, the `'error'` event will be emitted with
the exception.

这个函数是异步函数。当发生 `'connect'`事件时socket被建立，如果连接遇到问题， `'connect'`事件不会被触发，而携带异常信息的`'error'` 事件将被触发。
 

The `callback` parameter will be added as an listener for the 'connect'
event.

参数`callback`将作为 `connect`事件的监听器被增加进来。

#### socket.bufferSize

`net.Socket` has the property that `socket.write()` always works. This is to
help users get up an running quickly. The computer cannot necessarily keep up
with the amount of data that is written to a socket - the network connection simply
might be too slow. Node will internally queue up the data written to a socket and
send it out over the wire when it is possible. (Internally it is polling on
the socket's file descriptor for being writable).

`net.Socket`有一个特性，那就是`socket.write()`随时可用，这是为了使程序更快的运行。计算机发送数据的速度可能无法跟上程序向socket写入数据的速度——考虑网络速度很慢的情况。Node在内部维护了一个队列用于保存写入socket的数据，并在网络允许的时候将队列中的数据发送出去。（内部实现是探测socket的文件描述符是否可写。）

The consequence of this internal buffering is that memory may grow. This
property shows the number of characters currently buffered to be written.
(Number of characters is approximately equal to the number of bytes to be
written, but the buffer may contain strings, and the strings are lazily
encoded, so the exact number of bytes is not known.)

这种内部缓冲区的机制会增加内存消耗。此属性显示当前被缓冲的待写入字符数量。（字符的数量约等于字节数，但缓冲区中可能包含字符串，而字符串是延迟编码的，因此精确的字节数不可知。）

Users who experience large or growing `bufferSize` should attempt to
"throttle" the data flows in their program with `pause()` and `resume()`.

用户可以在程序中使用`pause()`和`resume()`来"截流"数据流，以控制大量或不断增长的`bufferSize`。

#### socket.setEncoding(encoding=null)

Sets the encoding (either `'ascii'`, `'utf8'`, or `'base64'`) for data that is
received.

设置接收到的数据的编码(可以是`'ascii'`，`'utf8'`或`'base64'`) 。

#### socket.setSecure()

This function has been removed in v0.3. It used to upgrade the connection to
SSL/TLS. See the TLS for the new API.

该函数用来将连接升级到SSL/TLS，在v0.3版本已经被废弃。参考TLS中新的API说明。


#### socket.write(data, [encoding], [callback])

Sends data on the socket. The second parameter specifies the encoding in the
case of a string--it defaults to UTF8 encoding.

向socket发送数据，第二个参数指定在发送字符串数据时的编码方式，默认的是UTF8编码。

Returns `true` if the entire data was flushed successfully to the kernel
buffer. Returns `false` if all or part of the data was queued in user memory.
`'drain'` will be emitted when the buffer is again free.

在所有数据被成功的写入系统内核缓冲区时返回`true`，如果全部或部分数据进入了用户内存的队列则返回`false`。
当缓冲区再次变空时，`'drain'` 事件将被触发。

The optional `callback` parameter will be executed when the data is finally
written out - this may not be immediately.

可选参数`callback` 将在数据最终被写出时执行——可能不是立即执行。

#### socket.write(data, [encoding], [fileDescriptor], [callback])

For UNIX sockets, it is possible to send a file descriptor through the
socket. Simply add the `fileDescriptor` argument and listen for the `'fd'`
event on the other end.

对于UNIX socket，可以通过socket发送一个文件描述符，简单的增加参数 `fileDescriptor`，并在另一端监听`'fd'`事件。

#### socket.end([data], [encoding])

Half-closes the socket. I.E., it sends a FIN packet. It is possible the
server will still send some data.

发送一个FIN数据包，关闭socket半连接。服务器仍可能发送一些数据。

If `data` is specified, it is equivalent to calling `socket.write(data, encoding)`
followed by `socket.end()`.

如果指定了`data` ，等同于依次调用`socket.write(data, encoding)`和`socket.end()`。


#### socket.destroy()

Ensures that no more I/O activity happens on this socket. Only necessary in
case of errors (parse error or so).

确保该socket上不再有活动的I/O操作，仅在发生错误的情况下需要（如解析错误等）。

#### socket.pause()

Pauses the reading of data. That is, `'data'` events will not be emitted.
Useful to throttle back an upload.

暂停读取数据，`'data'`将不会被触发，便于控制上传速度。

#### socket.resume()

Resumes reading after a call to `pause()`.

用于在调用`pause()`后，恢复读取数据。

#### socket.setTimeout(timeout, [callback])

Sets the socket to timeout after `timeout` milliseconds of inactivity on
the socket. By default `net.Socket` do not have a timeout.

设置socket不活动时间超过`timeout` 毫秒后进入超时状态。默认情况下`net.Socket`不会超时。


When an idle timeout is triggered the socket will receive a `'timeout'`
event but the connection will not be severed. The user must manually `end()`
or `destroy()` the socket.

当闲置超时发生时，socket会接收到一个`'timeout'`事件，但是连接不会被断开，用户必须手动的`end()`或`destroy()`该socket。


If `timeout` is 0, then the existing idle timeout is disabled.

如果 `timeout`设置成0，已经存在的闲置超时将被禁用。

The optional `callback` parameter will be added as a one time listener for the `'timeout'` event.

可选参数`callback` 将作为一次性监听器添加到 `'timeout'` 事件。

#### socket.setNoDelay(noDelay=true)

Disables the Nagle algorithm. By default TCP connections use the Nagle
algorithm, they buffer data before sending it off. Setting `noDelay` will
immediately fire off data each time `socket.write()` is called.

禁用Nagle算法。默认情况下TCP连接使用Nagle算法，在发送数据之前缓存它们。设置`noDelay`将使每次`socket.write()`调用都实时发送数据。

#### socket.setKeepAlive(enable=false, [initialDelay])

Enable/disable keep-alive functionality, and optionally set the initial
delay before the first keepalive probe is sent on an idle socket.
Set `initialDelay` (in milliseconds) to set the delay between the last
data packet received and the first keepalive probe. Setting 0 for
initialDelay will leave the value unchanged from the default
(or previous) setting.

开启或禁用keep-alive（连接保持）功能。可选择设置在一个闲置socket上第一次发送存活探测之前的延迟时间。设置`initialDelay`（单位毫秒）以设置最后从一个数据包接收到第一个存活探测发送之间的延时。将initialDelay设为0将不改变此参数默认（或之前）的设置。

#### socket.remoteAddress

The string representation of the remote IP address. For example,
`'74.125.127.100'` or `'2001:4860:a005::68'`.

以字符串形式表示的远端设备IP地址。例如：`'74.125.127.100'`或`'2001:4860:a005::68'`。

This member is only present in server-side connections.

此成员只存在于服务器端连接中。

#### Event: 'connect' 事件：'connect'

`function () { }`

Emitted when a socket connection successfully is established.
See `connect()`.

当一个socket连接成功建立时触发，参考 `connect()`。

#### Event: 'data' 事件：'data'

`function (data) { }`

Emitted when data is received.  The argument `data` will be a `Buffer` or
`String`.  Encoding of data is set by `socket.setEncoding()`.
(See the section on `Readable Socket` for more information.)

当收到数据时触发，参数`data`将是一个缓冲区（`Buffer`）或者字符串（`String`）。数据的编码方式通过`socket.setEncoding()`设置。（ 更多信息请参考章节`Readable Socket`）

#### Event: 'end' 事件：'end'

`function () { }`

Emitted when the other end of the socket sends a FIN packet.

当socket的远端发送了一个FIN数据包时触发。

By default (`allowHalfOpen == false`) the socket will destroy its file
descriptor  once it has written out its pending write queue.  However, by
setting `allowHalfOpen == true` the socket will not automatically `end()`
its side allowing the user to write arbitrary amounts of data, with the
caveat that the user is required to `end()` their side now.

默认情况下（`allowHalfOpen == false`）一旦待写出队列中的内容全部被写出，socket将自动销毁它的文件描述符。然而如果设置`allowHalfOpen == true`，则socket不会自动调用`end()`，而是允许用户继续写入任意数量的数据，这种情况下需要用户主动调用`end()`关闭半连接。

#### Event: 'timeout' 事件：'timeout'

`function () { }`

Emitted if the socket times out from inactivity. This is only to notify that
the socket has been idle. The user must manually close the connection.

当socket闲置超时情况下触发，它只是用来通知那个socket已经空闲，用户必须手动的关闭该连接。

See also: `socket.setTimeout()`

参考: `socket.setTimeout()`

#### Event: 'drain' 事件：'drain'

`function () { }`

Emitted when the write buffer becomes empty. Can be used to throttle uploads.

当缓冲区变空时触发，可以被用来调节上传速度。

#### Event: 'error' 事件：'error'

`function (exception) { }`

Emitted when an error occurs.  The `'close'` event will be called directly
following this event.

当有错误发生时触发， `'close'` 事件紧跟其后被调用。

#### Event: 'close' 事件：'close'

`function (had_error) { }`

Emitted once the socket is fully closed. The argument `had_error` is a boolean
which says if the socket was closed due to a transmission error.

当连接字完全被关闭时触发，参数`had_error`是一个布尔型变量， 用来说明连接字是否由于一个传输错误而关闭。

---

### net.isIP

#### net.isIP(input)

Tests if input is an IP address. Returns 0 for invalid strings,
returns 4 for IP version 4 addresses, and returns 6 for IP version 6 addresses.

测试输入参数是否是一个IP地址，如果是一个无效的字符串，返回0；如果是IPv4地址，返回4；如果是IPv6地址，返回6。

#### net.isIPv4(input)

Returns true if input is a version 4 IP address, otherwise returns false.

如果input是一个IPv4地址则返回true，否则返回false。

#### net.isIPv6(input)

Returns true if input is a version 6 IP address, otherwise returns false.

如果input是一个IPv6地址则返回true，否则返回false。

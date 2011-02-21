## UDP / Datagram Sockets

Datagram sockets are available through `require('dgram')`.  Datagrams are most commonly
handled as IP/UDP messages but they can also be used over Unix domain sockets.

通过执行`require('dgram')`用户数据报套接字功能就可以使用了。数据报通常以IP/UDP消息的形式处理，
同时它也可用于Unix域的套接字接口

### Event: 'message'

`function (msg, rinfo) { }`

Emitted when a new datagram is available on a socket.  `msg` is a `Buffer` and `rinfo` is
an object with the sender's address information and the number of bytes in the datagram.

当一个新的数据报载套接字上可用时派发.`msg`参数指向数据缓冲区，`rinfo`为一个对象，
它包含了发送者的地址信息以及数据表的自己大小。

### Event: 'listening'

`function () { }`

Emitted when a socket starts listening for datagrams.  This happens as soon as UDP sockets
are created.  Unix domain sockets do not start listening until calling `bind()` on them.

当套接字开始监听数据时派发。它发生在UDP套接字创建完毕的时候，不一样的是，Unix域套接字
直到调用`bind()`函数后才会开始监听。

### Event: 'close'

`function () { }`

Emitted when a socket is closed with `close()`.  No new `message` events will be emitted
on this socket.

当通过调用函数`close()`关闭一个套接字时派发。事件触发后该套接字将不会接收到新的消息事件。

### dgram.createSocket(type, [callback])

Creates a datagram socket of the specified types.  Valid types are:
`udp4`, `udp6`, and `unix_dgram`.

根据制定的类型创建数据报套接字，有效地类型包括：`udp4`, `udp6`, and `unix_dgram`。

Takes an optional callback which is added as a listener for `message` events.

接受一个可选的回调函数作为`message`的事件监听器

### dgram.send(buf, offset, length, path, [callback])

For Unix domain datagram sockets, the destination address is a pathname in the filesystem.
An optional callback may be supplied that is invoked after the `sendto` call is completed
by the OS.  It is not safe to re-use `buf` until the callback is invoked.  Note that
unless the socket is bound to a pathname with `bind()` there is no way to receive messages
on this socket.

对于Unix域数据报套接字，目的地址对应文件系统路径。提供的回调函数将在`sendto`被系统调用完成后执行。
在回调函数执行之前重新使用`buf`是不安全的。注意服务器端必须通过`bind()`函数将套接字绑定到文件系统路径上，
否则该套接字接收不到消息。


Example of sending a message to syslogd on OSX via Unix domain socket `/var/run/syslog`:

这是一个向基于OSX的Unix域发送消息的例子，套接字的文件系统路径为`/var/run/syslog`

    var dgram = require('dgram');
    var message = new Buffer("A message to log.");
    var client = dgram.createSocket("unix_dgram");
    client.send(message, 0, message.length, "/var/run/syslog",
      function (err, bytes) {
        if (err) {
          throw err;
        }
        console.log("Wrote " + bytes + " bytes to socket.");
    });

### dgram.send(buf, offset, length, port, address, [callback])

For UDP sockets, the destination port and IP address must be specified.  A string
may be supplied for the `address` parameter, and it will be resolved with DNS.  An
optional callback may be specified to detect any DNS errors and when `buf` may be
re-used.  Note that DNS lookups will delay the time that a send takes place, at
least until the next tick.  The only way to know for sure that a send has taken place
is to use the callback.

对UDP套接字来说，必须指定目的端口和IP地址，ip地址通过`address`参数以字符串形式提供，
并且提供的地址是可以被DNS所解析的。可选的回调函数可以用于检测是否发生了DNS错误
以及`buf`是否被重新使用。注意DNS查询将会延迟send函数发生的时间，而唯一能确定send
是否发生的方法就是使用回调函数进行检测。

Example of sending a UDP packet to a random port on `localhost`;

这是一个向本地随机端口发送UDP包的示例。

    var dgram = require('dgram');
    var message = new Buffer("Some bytes");
    var client = dgram.createSocket("udp4");
    client.send(message, 0, message.length, 41234, "localhost");
    client.close();


### dgram.bind(path)

For Unix domain datagram sockets, start listening for incoming datagrams on a
socket specified by `path`. Note that clients may `send()` without `bind()`,
but no datagrams will be received without a `bind()`.

对于Unix服务器来说，在套接字上开始监听数据必须指定路径`path`。
注意客户端在执行`send()`时可能没有调用`bind()`函数绑定套接字，这样会导致客户端套接字无法接收到数据

Example of a Unix domain datagram server that echoes back all messages it receives:

这是一个Unix域数据报服务端的例子，它将接收到的所有信息原封不动的返回给客户端

    var dgram = require("dgram");
    var serverPath = "/tmp/dgram_server_sock";
    var server = dgram.createSocket("unix_dgram");

    server.on("message", function (msg, rinfo) {
      console.log("got: " + msg + " from " + rinfo.address);
      server.send(msg, 0, msg.length, rinfo.address);
    });

    server.on("listening", function () {
      console.log("server listening " + server.address().address);
    })

    server.bind(serverPath);

Example of a Unix domain datagram client that talks to this server:

这是Unix域数据报客户端与服务器端通信的例子

    var dgram = require("dgram");
    var serverPath = "/tmp/dgram_server_sock";
    var clientPath = "/tmp/dgram_client_sock";

    var message = new Buffer("A message at " + (new Date()));

    var client = dgram.createSocket("unix_dgram");

    client.on("message", function (msg, rinfo) {
      console.log("got: " + msg + " from " + rinfo.address);
    });

    client.on("listening", function () {
      console.log("client listening " + client.address().address);
      client.send(message, 0, message.length, serverPath);
    });

    client.bind(clientPath);

### dgram.bind(port, [address])

For UDP sockets, listen for datagrams on a named `port` and optional `address`.  If
`address` is not specified, the OS will try to listen on all addresses.

对UDP套接字来说，套接字将会监听来自端口port和地址address的数据，如果`address`未指定，操作
系统将尝试监听所有的地址。

Example of a UDP server listening on port 41234:

    var dgram = require("dgram");

    var server = dgram.createSocket("udp4");
    var messageToSend = new Buffer("A message to send");

    server.on("message", function (msg, rinfo) {
      console.log("server got: " + msg + " from " +
        rinfo.address + ":" + rinfo.port);
    });

    server.on("listening", function () {
      var address = server.address();
      console.log("server listening " +
          address.address + ":" + address.port);
    });

    server.bind(41234);
    // server listening 0.0.0.0:41234


### dgram.close()

Close the underlying socket and stop listening for data on it.  UDP sockets
automatically listen for messages, even if they did not call `bind()`.

关闭套接字并停止监听该套接字上的数据。
注意即使没有通过`bind()`进行绑定，UDP套接字也会自动监听所有的消息。

### dgram.address()

Returns an object containing the address information for a socket.  For UDP sockets,
this object will contain `address` and `port`.  For Unix domain sockets, it will contain
only `address`.

返回包含套接字地址信息的对象。对UDP套接字来说，该对象包含属性`address`和`port`，
而对Unix域的套接字来说仅包含`address`。

### dgram.setBroadcast(flag)

Sets or clears the `SO_BROADCAST` socket option.  When this option is set, UDP packets
may be sent to a local interface's broadcast address.

设置或者清除套接字的`SO_BROADCAST`（广播）选项。当该设置生效时，UDP包将被发送至本地接口的
广播地址


### dgram.setTTL(ttl)

Sets the `IP_TTL` socket option.  TTL stands for "Time to Live," but in this context it
specifies the number of IP hops that a packet is allowed to go through.  Each router or
gateway that forwards a packet decrements the TTL.  If the TTL is decremented to 0 by a
router, it will not be forwarded.  Changing TTL values is typically done for network
probes or when multicasting.

设置套接字的`IP_TTL`选项。TTL全称"Time to Live"，原指存活时间，但在这里它指定了数据报
在网络上允许经过的IP跳数。如果数据报的TTL值到达路由时递减至0，那么它将不会继续传播。
当进行多路传播或者进行网络预测时通常需要修改TTL值。

The argument to `setTTL()` is a number of hops between 1 and 255.  The default on most
systems is 64.

`setTTL()`的参数指明了从1至255的跳数，操作系统的默认值为64。

### dgram.setMulticastTTL(ttl)

Sets the `IP_MULTICAST_TTL` socket option.  TTL stands for "Time to Live," but in this
context it specifies the number of IP hops that a packet is allowed to go through,
specifically for multicast traffic.  Each router or gateway that forwards a packet
decrements the TTL. If the TTL is decremented to 0 by a router, it will not be forwarded.

设置套接字的`IP_MULTICAST_TTL`选项。TTL全称"Time to Live"，原指存活时间，但在这里它指定了数据报
在多路传播的网络上允许经过的IP跳数。数据包没经过一个路由器或者网关都会递减。如果TTL值到达某个路
由器时递减至0，那么该数据包将不会继续传播。

The argument to `setMulticastTTL()` is a number of hops between 0 and 255.  The default on most
systems is 64.

`setMulticastTTL()`的参数指明了从1至255的跳数，操作系统的默认值为64。

### dgram.setMulticastLoopback(flag)

Sets or clears the `IP_MULTICAST_LOOP` socket option.  When this option is set, multicast
packets will also be received on the local interface.

设置套接字的`IP_MULTICAST_LOOP`选项。当该选项生效时，多路传播的数据报也会被本地接口接收到。

### dgram.addMembership(multicastAddress, [multicastInterface])

Tells the kernel to join a multicast group with `IP_ADD_MEMBERSHIP` socket option.

该方法告诉内核地址加入到多路传播地址组中，并设置`IP_ADD_MEMBERSHIP`套接字选项

If `multicastAddress` is not specified, the OS will try to add membership to all valid
interfaces.

如果`multicastAddress`没有指定，操作系统会尝试将该地址加入到

### dgram.dropMembership(multicastAddress, [multicastInterface])

Opposite of `addMembership` - tells the kernel to leave a multicast group with
`IP_DROP_MEMBERSHIP` socket option. This is automatically called by the kernel
when the socket is closed or process terminates, so most apps will never need to call
this.

与`addMembership`相反，该方法告诉内核清除套接字的`IP_DROP_MEMBERSHIP`选项。
当套接字关闭或进程销毁时该操作会自动执行，所以大部分的应用不需要手动调用该方法。

If `multicastAddress` is not specified, the OS will try to drop membership to all valid
interfaces.

如果`multicastAddress`没有指定，操作系统会尝试将所有有效接口

## UDP / Datagram Sockets 数据报套接字模块

Datagram sockets are available through `require('dgram')`.  Datagrams are most commonly
handled as IP/UDP messages but they can also be used over Unix domain sockets.

要使用数据报套接字模块需要调用`require('dgram')`。数据报通常作为IP/UDP消息来处理，但它也可用于Unix域套接字。 

### Event: 'message' 事件：'message'

`function (msg, rinfo) { }`

Emitted when a new datagram is available on a socket.  `msg` is a `Buffer` and `rinfo` is
an object with the sender's address information and the number of bytes in the datagram.

当套接字接收到一个新的数据报的时候触发此事件。`msg`是一个`Buffer`缓冲器，`rinfo`是一个包含了发送方地址信息以及数据报字节长度的对象。

### Event: 'listening' 事件：'listening'

`function () { }`

Emitted when a socket starts listening for datagrams.  This happens as soon as UDP sockets
are created.  Unix domain sockets do not start listening until calling `bind()` on them.

当一个套接字开始监听数据报的时候触发。一旦UDP套接字建立后就会触发这个事件。而Unix域套接字直到调用了`bind()`方法才会开始监听。

### Event: 'close' 事件：'close'

`function () { }`

Emitted when a socket is closed with `close()`.  No new `message` events will be emitted
on this socket.

当调用`close()`方法关闭一个套接字时触发此事件。此后不会再有新的`message`事件在此套接字上发生。

### dgram.createSocket(type, [callback])

Creates a datagram socket of the specified types.  Valid types are:
`udp4`, `udp6`, and `unix_dgram`.

建立一个指定类型的数据报套接字，有效类型有：`udp4`，`udp6`，以及`unix_dgram`。 

Takes an optional callback which is added as a listener for `message` events.

可选参数callback指定了`message`事件的监听器回调函数。 

### dgram.send(buf, offset, length, path, [callback])

For Unix domain datagram sockets, the destination address is a pathname in the filesystem.
An optional callback may be supplied that is invoked after the `sendto` call is completed
by the OS.  It is not safe to re-use `buf` until the callback is invoked.  Note that
unless the socket is bound to a pathname with `bind()` there is no way to receive messages
on this socket.

对于Unix域数据报套接字而言，目标地址是文件系统中的一个路径。可选参数callback指定了系统调用`sendto`完成后的回调函数。在回调函数callback执行前重复使用buf是很不安全的。要注意除非这个套接字已经使用`bind()`方法绑定到一个路径上，否则这个套接字无法接收到任何信息。 


Example of sending a message to syslogd on OSX via Unix domain socket `/var/run/syslog`:

一个在OSX系统上，通过Unix域套接字`/var/run/syslog`向syslogd发送消息的例子：

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

对于UDP套接字来说，目标端口和IP地址是必须要指定的。可以用字符串来指定`address`参数，这个参数通过DNS进行解析。可选参数callback指定一个回调函数，用于检测DNS解析错误以及什么时候`buf`可被重用。注意DNS查询将会使发送动作最少延迟到下一个时间片执行，如果你想确定发送动作是否发生，使用回调将是唯一的办法。

Example of sending a UDP packet to a random port on `localhost`;

下面是一个发送UDP数据包到`localhost`（本机）一个随机端口的例子：

    var dgram = require('dgram');
    var message = new Buffer("Some bytes");
    var client = dgram.createSocket("udp4");
    client.send(message, 0, message.length, 41234, "localhost");
    client.close();


### dgram.bind(path)

For Unix domain datagram sockets, start listening for incoming datagrams on a
socket specified by `path`. Note that clients may `send()` without `bind()`,
but no datagrams will be received without a `bind()`.

对Unix域数据报套接字来说，通过指定一个`path`（路径）开始在套接字上监听数据报。注意客户端可以无需调用`bind()`直接使用`send()`方法发送数据报，但是不使用`bind()`方法将无法接收到任何数据报。 

Example of a Unix domain datagram server that echoes back all messages it receives:

下面是一个Unix域数据报服务器的例子，此服务器将接收到的所有消息原样返回： 

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

下面是一个与上述服务器通信的Unix域数据报客户端的例子： 

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

对于UDP套接字而言，该方法会在`port`指定的端口和可选地址 `address`上监听数据报。如果`address`没有指定，则操作系统会监听所有有效地址。 

Example of a UDP server listening on port 41234:

下面是一个监听在41234端口的UDP服务器的例子：

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

该函数关闭底层的套接字并停止在其上监听数据。UDP套接字在没有调用`bing()`方法的情况下也自动监听消息。

### dgram.address()

Returns an object containing the address information for a socket.  For UDP sockets,
this object will contain `address` and `port`.  For Unix domain sockets, it will contain
only `address`.

返回包含套接字地址信息的对象。对于UDP套接字来说，这个对象将包含`address`（地址）和`port`（端口）。对于UNIX域套接字来说，这个对象仅包含`address`（地址）。

### dgram.setBroadcast(flag)

Sets or clears the `SO_BROADCAST` socket option.  When this option is set, UDP packets
may be sent to a local interface's broadcast address.

设置或者清除套接字的`SO_BROADCAST`（广播）选项。当该设置生效时，UDP数据包将被发送至本地网络接口的广播地址。

### dgram.setTTL(ttl)

Sets the `IP_TTL` socket option.  TTL stands for "Time to Live," but in this context it
specifies the number of IP hops that a packet is allowed to go through.  Each router or
gateway that forwards a packet decrements the TTL.  If the TTL is decremented to 0 by a
router, it will not be forwarded.  Changing TTL values is typically done for network
probes or when multicasting.

设置套接字的`IP_TTL`选项，TTL表示“存活时间”，但是在这个上下文环境中，特指一个数据包允许经过的IP跳数。每经过一个路由器或者网关TTL的值都会减一，如果TTL被一个路由器减少到0，这个数据包将不会继续转发。在网络探针或组播应用中会需要修改TTL数值。

The argument to `setTTL()` is a number of hops between 1 and 255.  The default on most
systems is 64.

在`setTTL()`调用中设置的跳数介于1到255之间，大多数系统缺省会设置为64。 

### dgram.setMulticastTTL(ttl)

Sets the `IP_MULTICAST_TTL` socket option.  TTL stands for "Time to Live," but in this
context it specifies the number of IP hops that a packet is allowed to go through,
specifically for multicast traffic.  Each router or gateway that forwards a packet
decrements the TTL. If the TTL is decremented to 0 by a router, it will not be forwarded.

设置套接字的`IP_MULTICAST_TTL`选项。TTL全称"Time to Live"，原指存活时间，但在这里特指在组播通信中数据包允许经过的IP跳数。每经过一个路由器或者网关TTL的值都会减一。如果TTL被一个路由器减少到0，那么该数据包将不会继续传播。

The argument to `setMulticastTTL()` is a number of hops between 0 and 255.  The default on most
systems is 64.

`setMulticastTTL()`的参数为1至255之间的跳数，大部分操作系统的默认值为64。

### dgram.setMulticastLoopback(flag)

Sets or clears the `IP_MULTICAST_LOOP` socket option.  When this option is set, multicast
packets will also be received on the local interface.

设置或清除套接字的`IP_MULTICAST_LOOP`选项。当该选项生效时，多路传播的数据包也会被本地网络接口接收到。

### dgram.addMembership(multicastAddress, [multicastInterface])

Tells the kernel to join a multicast group with `IP_ADD_MEMBERSHIP` socket option.

该方法通知系统内核使用`IP_ADD_MEMBERSHIP`套接字选项将套接字加入一个组播组。

If `multicastAddress` is not specified, the OS will try to add membership to all valid
interfaces.

如果没有指定`multicastInterface`参数，操作系统将尝试把所有有效的网络接口加入组播组。

### dgram.dropMembership(multicastAddress, [multicastInterface])

Opposite of `addMembership` - tells the kernel to leave a multicast group with
`IP_DROP_MEMBERSHIP` socket option. This is automatically called by the kernel
when the socket is closed or process terminates, so most apps will never need to call
this.

与`addMembership`相反，该方法通知系统内核使用`IP_DROP_MEMBERSHIP`选项使套接字脱离组播组。由于当套接字关闭或进程终止时该操作会自动执行，所以大部分的应用不需要手动调用该方法。

If `multicastAddress` is not specified, the OS will try to drop membership to all valid
interfaces.

如果`multicastInterface`没有指定，操作系统会尝试将所有可用网络接口从组播组里脱离。

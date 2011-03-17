## UDP / Datagram Sockets

Datagram sockets are available through `require('dgram')`.  Datagrams are most commonly
handled as IP/UDP messages but they can also be used over Unix domain sockets.

要使用数据包SOCKET需要调用require('dgram')，数据报一般用来处理IP/UDP信息，
但是数据报也可用在UNIX DOMAIN SOCKETS上 

### Event: 'message'

`function (msg, rinfo) { }`

Emitted when a new datagram is available on a socket.  `msg` is a `Buffer` and `rinfo` is
an object with the sender's address information and the number of bytes in the datagram.

当一个SOCKET接收到一个新的数据包的时候触发此事件，msg是缓冲区变量,rinfo是一个包含了发送者
地址信息以及数据报字节长度的对象

### Event: 'listening'

`function () { }`

Emitted when a socket starts listening for datagrams.  This happens as soon as UDP sockets
are created.  Unix domain sockets do not start listening until calling `bind()` on them.

当一个SOCKET开始监听数据报的时候触发，当UDP SOCKET建立后就会触发这个事件。而UNIX DOMAIN SOCKET
直到在SOCKET上调用了bind()方法才会触发这个消息.

### Event: 'close'

`function () { }`

Emitted when a socket is closed with `close()`.  No new `message` events will be emitted
on this socket.

当一个SOCKET使用close()方法关闭时触发此事件.在此事件之后此SOCKET不会有任何消息事件被触发。

### dgram.createSocket(type, [callback])

Creates a datagram socket of the specified types.  Valid types are:
`udp4`, `udp6`, and `unix_dgram`.

建立一个指定类型的数据报SOCKET,有效类型有:udp4,udp6,unix_dgram 

Takes an optional callback which is added as a listener for `message` events.

callback作为一个可选项，可作为message事件的监听器被加入 

### dgram.send(buf, offset, length, path, [callback])

For Unix domain datagram sockets, the destination address is a pathname in the filesystem.
An optional callback may be supplied that is invoked after the `sendto` call is completed
by the OS.  It is not safe to re-use `buf` until the callback is invoked.  Note that
unless the socket is bound to a pathname with `bind()` there is no way to receive messages
on this socket.

对于unix domain datagram xockets来说,他的目标地址是一个使用文件系统表示的路径名,callback作为
一个可选项会在系统调用sendto完毕后被触发。除非 callback被触发，否则重复使用buf是很不安全的。
要注意除非这个socket已经使用bind()方法绑定到一个路径名上，否则这个 SOCKET无法接收到任何信息。 


Example of sending a message to syslogd on OSX via Unix domain socket `/var/run/syslog`:

下面是一个通过unix domain socket /var/run/syslog发送消息到syslogd的例子： 

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
    
从MESSAGE中偏移为0的地方开始，长度为MESSAGE.LENGTH的这些内容通过/var/run/syslog发送系统调用发送后，将调用CALLBACK，
如果有错误则抛出异常，否则console.log实际发送了多少个字节 

### dgram.send(buf, offset, length, port, address, [callback])

For UDP sockets, the destination port and IP address must be specified.  A string
may be supplied for the `address` parameter, and it will be resolved with DNS.  An
optional callback may be specified to detect any DNS errors and when `buf` may be
re-used.  Note that DNS lookups will delay the time that a send takes place, at
least until the next tick.  The only way to know for sure that a send has taken place
is to use the callback.

对于UDPSOCKETS来说，目标端口和IP地址是必须要指定的，可以用字符串来指定地址参数，并且
这个参数是可以通过DNS解析的，CALLBACK 作为可选项可以检测到任何DNS错误和是否BUF重复使用了。
请记住DNS查询将会使SEND动作最少延迟到下一个执行时间片发生，如果你想确定SEND方法是否发生，
使用CALLBACK将是唯一的办法

Example of sending a UDP packet to a random port on `localhost`;

下面是一个发送UDP数据包到本机一个随机端口的例子。

    var dgram = require('dgram');
    var message = new Buffer("Some bytes");
    var client = dgram.createSocket("udp4");
    client.send(message, 0, message.length, 41234, "localhost");
    client.close();


### dgram.bind(path)

For Unix domain datagram sockets, start listening for incoming datagrams on a
socket specified by `path`. Note that clients may `send()` without `bind()`,
but no datagrams will be received without a `bind()`.

只有在Unxi DOMAIN DATAGRAM SOCKET中使用，该函数开始在一个指定路径上监听一个SOCKET过来的的数据报。
要记得，客户端可以在没有调用BIND()方法就直接调用SEND()方法，但是不使用BIND()方法是无法接收到任何信息的。 

Example of a Unix domain datagram server that echoes back all messages it receives:

下面是一个使用UNIX DOMAIN 数据包服务器来做接受信息回显的例子： 

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

下面是一个UNIX DOMAIN DATAGRAM 客户端与服务器交互的例子 

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

对于UDP SOCKETS，这个方法会在指定端口和可选地址上监听，如果地址没有指定，
则系统会尝试监听所有有效地址。 

Example of a UDP server listening on port 41234:

下面是一个监听在41234端口的UDP服务器的例子 

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

这个方法关闭非延迟的SOCKET并且停止在其上监听数据。即使没有调用BIND()方法UDP SOCKET也会自动监听消息，

### dgram.address()

Returns an object containing the address information for a socket.  For UDP sockets,
this object will contain `address` and `port`.  For Unix domain sockets, it will contain
only `address`.

返回包含SOCKET地址信息的一个对象，对于UDP SOCKETS来说，这个对象将包含地址和端口，
对于UNIX DOMAIN SOCKETS来说，这个对象仅包含地址 

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

设置IP_TTL这个选项，TTL表示“存活时间”，但是在这个上下文环境中，他也可以指定IP的HOPS（
每个节点在转发数据包时的消耗。如果 Hop limit 消耗到0，则取消数据包）来确定一个数据包大致允许经过多少节点。
每经过个路由器或者网关都会减少TTL数值，如果TTL被一个路由器减少到0，这个数据报将不会继续转发，
修改TTL数值经常用来当网络探针或者作为数据多播使用 

The argument to `setTTL()` is a number of hops between 1 and 255.  The default on most
systems is 64.

ttl用来设置HOPS的数值从1到255，大多数系统缺省会设置为64 

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

该方法告诉内核将数据报添加到多路传播地址组中，并设置`IP_ADD_MEMBERSHIP`套接字选项

If `multicastAddress` is not specified, the OS will try to add membership to all valid
interfaces.

如果`multicastAddress`没有指定，操作系统会尝试将该数据报添加到所有有效地有效的接口中

### dgram.dropMembership(multicastAddress, [multicastInterface])

Opposite of `addMembership` - tells the kernel to leave a multicast group with
`IP_DROP_MEMBERSHIP` socket option. This is automatically called by the kernel
when the socket is closed or process terminates, so most apps will never need to call
this.

与`addMembership`相反，该方法告诉系统内核清除套接字的`IP_DROP_MEMBERSHIP`选项。
由于当套接字关闭或进程销毁时该操作会自动执行，所以大部分的应用不需要手动调用该方法。

If `multicastAddress` is not specified, the OS will try to drop membership to all valid
interfaces.

如果`multicastAddress`没有指定，操作系统会尝试将数据报从所有有效接口中丢弃掉

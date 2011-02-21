## DNS


Use `require('dns')` to access this module.

使用 `require('util')` 去访问这个模块

Here is an example which resolves `'www.google.com'` then reverse
resolves the IP addresses which are returned.

下面这个例子首先将`'www.google.com'`解析为ip地址，再对返回的ip地址做反向解析

    var dns = require('dns');

    dns.resolve4('www.google.com', function (err, addresses) {
      if (err) throw err;

      console.log('addresses: ' + JSON.stringify(addresses));

      addresses.forEach(function (a) {
        dns.reverse(a, function (err, domains) {
          if (err) {
            console.log('reverse for ' + a + ' failed: ' +
              err.message);
          } else {
            console.log('reverse for ' + a + ': ' +
              JSON.stringify(domains));
          }
        });
      });
    });

### dns.lookup(domain, family=null, callback)

Resolves a domain (e.g. `'google.com'`) into the first found A (IPv4) or
AAAA (IPv6) record.

解析域名比如说将 `'google.com'` 转换为第一个查找到的资源记录类型为A （即IPv4） 或者
资源记录类型为 AAAA（即IPv6）的记录值

The callback has arguments `(err, address, family)`.  The `address` argument
is a string representation of a IP v4 or v6 address. The `family` argument
is either the integer 4 or 6 and denotes the family of `address` (not
neccessarily the value initially passed to `lookup`).

在lookup函数中callback对应的回调函数包含三个参数`(err, address, family)`。其中`address`参数
以字符串形式表示返回的IPv4或者IPv6的地址，`family`参数为一个整型变量指明了`address`参数表示
的地址族的类型（传给lookup函数中的family变量是非必须的）。

### dns.resolve(domain, rrtype='A', callback)

Resolves a domain (e.g. `'google.com'`) into an array of the record types
specified by rrtype. Valid rrtypes are `A` (IPV4 addresses), `AAAA` (IPV6
addresses), `MX` (mail exchange records), `TXT` (text records), `SRV` (SRV
records), and `PTR` (used for reverse IP lookups).

将域名（比如说`'google.com'`）解析到一个数组中，数组中记录类型由指定的
rrtype变量值（资源记录类型）决定。rrtypes参数的有效值包括`A` (IPV4地址), `AAAA` (IPV6
地址), `MX` (邮件交换记录), `TXT` (text records), `SRV` (SRV
records), and `PTR` (用于ip反向查找)。

The callback has arguments `(err, addresses)`.  The type of each item
in `addresses` is determined by the record type, and described in the
documentation for the corresponding lookup methods below.

回调函数callback包含两个参数`(err, addresses)`。其中addresses参数（addresses是一个数组）
中的每个值由记录类型决定，和这些记录类型相关的调用方法将在下面一一列出。

On error, `err` would be an instanceof `Error` object, where `err.errno` is
one of the error codes listed below and `err.message` is a string describing
the error in English.

一旦出现错误，`err` 标识`Error`对象实例，其中属性`err.errno`表示导致错误的代码值，
你可以在文档最后查看到所有的错误代码，属性`err.message`用英语描述了导致错误的信息。

### dns.resolve4(domain, callback)

The same as `dns.resolve()`, but only for IPv4 queries (`A` records).
`addresses` is an array of IPv4 addresses (e.g.
`['74.125.79.104', '74.125.79.105', '74.125.79.106']`).

类似`dns.resolve()`，但只针对IPv4的查询（`A` records）。
`addresses`以数组形式表示返回的IPv4地址，如下所示
（`['74.125.79.104', '74.125.79.105', '74.125.79.106']`）。

### dns.resolve6(domain, callback)

The same as `dns.resolve4()` except for IPv6 queries (an `AAAA` query).

类似`dns.resolve4()`，区别在于仅针对IPv6地址的查询 (an `AAAA` query)。

### dns.resolveMx(domain, callback)

The same as `dns.resolve()`, but only for mail exchange queries (`MX` records).

类似`dns.resolve()`，但仅邮件交换记录的查询(`MX` records)。

`addresses` is an array of MX records, each with a priority and an exchange
attribute (e.g. `[{'priority': 10, 'exchange': 'mx.example.com'},...]`).

`addresses`以数组形式表示返回的MX记录，其中数组中的每一个对象均包含priority和exchange
两个属性，如下所示（`[{'priority': 10, 'exchange': 'mx.example.com'},...]`）。

### dns.resolveTxt(domain, callback)

The same as `dns.resolve()`, but only for text queries (`TXT` records).
`addresses` is an array of the text records available for `domain` (e.g.,
`['v=spf1 ip4:0.0.0.0 ~all']`).

类似`dns.resolve()`，但仅用于text记录的查询(`TXT` records)。
`addresses`以数组形式返回可用于指定域的TXT记录值，
如下所示(`['v=spf1 ip4:0.0.0.0 ~all']`)。

### dns.resolveSrv(domain, callback)

The same as `dns.resolve()`, but only for service records (`SRV` records).
`addresses` is an array of the SRV records available for `domain`. Properties
of SRV records are priority, weight, port, and name (e.g.,
`[{'priority': 10, {'weight': 5, 'port': 21223, 'name': 'service.example.com'}, ...]`).

类似`dns.resolve()`，但仅用于service records (`SRV` records)。
`addresses`以数组形式返回可用于指定域的SRV记录值，每个SRV记录包含
priority（优先级）, weight（权重）, port（端口号）和name（服务域名）四个属性
`[{'priority': 10,'weight': 5, 'port': 21223, 'name': 'service.example.com'}, ...]`).

### dns.reverse(ip, callback)

Reverse resolves an ip address to an array of domain names.

反向解析一个ip地址并以数组形式返回解析后的域名。

The callback has arguments `(err, domains)`.

回调函数包含的参数为 `(err, domains)`。

If there an an error, `err` will be non-null and an instanceof the Error
object.

如果出现了错误，`err`将变为非空值，指向Error对象的实例。

Each DNS query can return an error code.

每一个DNS查询可能返回的错误代码如下所示。

- `dns.TEMPFAIL`: timeout, SERVFAIL or similar.

- `dns.TEMPFAIL`: 超时, 访问服务失败或类似的情况.

- `dns.PROTOCOL`: got garbled reply.

- `dns.PROTOCOL`: 得到的答复为乱码？

- `dns.NXDOMAIN`: domain does not exists.

- `dns.NXDOMAIN`: 域名不存在.

- `dns.NODATA`: domain exists but no data of reqd type.

- `dns.NODATA`: 域名存在但没有reqd类型的信息.

- `dns.NOMEM`: out of memory while processing.

- `dns.NOMEM`: 操作过程中出现内存溢出.

- `dns.BADQUERY`: the query is malformed.

- `dns.BADQUERY`: 查询出现异常

原文勘误：

1.`[{'priority': 10, {'weight': 5, 'port': 21223, 'name': 'service.example.com'}, ...]`). 多了大括号{
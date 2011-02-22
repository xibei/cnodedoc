## DNS

Use `require('dns')` to access this module.

使用require('dns')来访问这个模块。 

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

将一个域名(例如. 'google.com')解析成为找到的第一个A(IPv4)或者AAAA(IPv6)记录。

The callback has arguments `(err, address, family)`.  The `address` argument
is a string representation of a IP v4 or v6 address. The `family` argument
is either the integer 4 or 6 and denotes the family of `address` (not
neccessarily the value initially passed to `lookup`).

调函数的有(err, address, family)这三个参数。address参数是一个代表IPv4或IPv6的地址的字符串。
family是一个表示地址版本的整形数字4或6(并不一定是解析域名时传递的数字)。

### dns.resolve(domain, rrtype='A', callback)

Resolves a domain (e.g. `'google.com'`) into an array of the record types
specified by rrtype. Valid rrtypes are `A` (IPV4 addresses), `AAAA` (IPV6
addresses), `MX` (mail exchange records), `TXT` (text records), `SRV` (SRV
records), and `PTR` (used for reverse IP lookups).

将参数domain(比如'google.com')按照参数rrtype所指定数据类型解析到一个数组中。
合法的类型为A（IPV4地址），AAAA（IPV6地址），MX（mail exchange records）,
TXT(text records)，SRV（SRV records），和PTR（used for reveres IP lookups）。 

The callback has arguments `(err, addresses)`.  The type of each item
in `addresses` is determined by the record type, and described in the
documentation for the corresponding lookup methods below.

回调函数（callback）接受两个参数：err和address。参数address中的每一项根据记录类型(record type)分割，
在下面lookup方法的文档里有详细的解释。 

On error, `err` would be an instanceof `Error` object, where `err.errno` is
one of the error codes listed below and `err.message` is a string describing
the error in English.

当有错误发生时，参数err的内容是一个Error对象的实例，err的errno属性是下面错误代码列表中的一个，
err的message属性是一个用英语表述的错误解释。 

### dns.resolve4(domain, callback)

The same as `dns.resolve()`, but only for IPv4 queries (`A` records).
`addresses` is an array of IPv4 addresses (e.g.
`['74.125.79.104', '74.125.79.105', '74.125.79.106']`).

与dns.resolve()类似,但是仅对IPV4地址进行查询(A records)。addresses是一个IPV4地址数组
(例如['74.125.79.104', '74.125.79.105', '74.125.79.106']) 

### dns.resolve6(domain, callback)

The same as `dns.resolve4()` except for IPv6 queries (an `AAAA` query).

除了这个函数是对IPV6地址的查询外与dns.resolve4()很类似(一个AAAA查询)。 

### dns.resolveMx(domain, callback)

The same as `dns.resolve()`, but only for mail exchange queries (`MX` records).

与dns.resolve()很类似.但是仅做mail exchange查询(MX类型记录)。 

`addresses` is an array of MX records, each with a priority and an exchange
attribute (e.g. `[{'priority': 10, 'exchange': 'mx.example.com'},...]`).

回调函数的参数address是一个MX类型记录的数组,每个记录有一个优先级属性和一个交换
属性(类似[{'priority': 10, 'exchange': 'mx.example.com'},...]) 

### dns.resolveTxt(domain, callback)

The same as `dns.resolve()`, but only for text queries (`TXT` records).
`addresses` is an array of the text records available for `domain` (e.g.,
`['v=spf1 ip4:0.0.0.0 ~all']`).

与dns.resolve()很相似,但是仅可以进行text查询(TXT记录).地址是一个对于域来说有效的text记录数组（类似 ['v=spf1 ip4:0.0.0.0 ~all']） 

### dns.resolveSrv(domain, callback)

The same as `dns.resolve()`, but only for service records (`SRV` records).
`addresses` is an array of the SRV records available for `domain`. Properties
of SRV records are priority, weight, port, and name (e.g.,
`[{'priority': 10, {'weight': 5, 'port': 21223, 'name': 'service.example.com'}, ...]`).

与dns.resolve()很类似,但仅是只查询service记录(srv records)。地址是一个对于域来说有效的SRV记录的数组，
SRV记录的属性有优先级、权重、端口，名字(例如 [{'priority': 10, {'weight': 5, 'port': 21223, 'name': 'service.example.com'}, ...]) 

### dns.reverse(ip, callback)

Reverse resolves an ip address to an array of domain names.

反向解析一个IP地址到一个域名数组。 

The callback has arguments `(err, domains)`.

回调函数包含的参数为 `(err, domains)`。

If there an an error, `err` will be non-null and an instanceof the Error
object.

如果发生了错误,err为Error对象的实例。 

Each DNS query can return an error code.

每个DNS查询可以返回如下错误代码： 

- `dns.TEMPFAIL`: timeout, SERVFAIL or similar.

- `dns.TEMPFAIL`: 超时、返回SERVFAIL或者类似的错误 

- `dns.PROTOCOL`: got garbled reply.

- `dns.PROTOCOL`: 返回内容里有乱码 

- `dns.NXDOMAIN`: domain does not exists.

- `dns.NXDOMAIN`: 域名不存在.

- `dns.NODATA`: domain exists but no data of reqd type.

- `dns.NODATA`: 域名存在但是没有所请求的查询类型的数据 

- `dns.NOMEM`: out of memory while processing.

- `dns.NOMEM`: 处理过程中内存溢出 

- `dns.BADQUERY`: the query is malformed.

- `dns.BADQUERY`: 查询语句出现异常

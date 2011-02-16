## URL URL模块

This module has utilities for URL resolution and parsing.
Call `require('url')` to use it.

此模块包含用于解析和分析URL的工具。可通过`require('url')`访问他们。

Parsed URL objects have some or all of the following fields, depending on
whether or not they exist in the URL string. Any parts that are not in the URL
string will not be in the parsed object. Examples are shown for the URL

解析后的URL对象包含下述部分或全部字段。具体包含哪些字段取决于解析前的URL字符串中是否存在这些字段。在原始的URL字符串中不存在的字段在解析后的对象中也不会包含。以下面这个URL为例：

`'http://user:pass@host.com:8080/p/a/t/h?query=string#hash'`

* `href`: The full URL that was originally parsed.

  `href`：完整的原始URL字符串。

  Example: `'http://user:pass@host.com:8080/p/a/t/h?query=string#hash'`

  例如：`'http://user:pass@host.com:8080/p/a/t/h?query=string#hash'`
* `protocol`: The request protocol.

  `protocol`：请求所使用的协议。

  Example: `'http:'`

  例如：`'http:'`
* `host`: The full host portion of the URL, including port and authentication information.

  `host`：URL中关于主机的完整信息，包括端口以及用户身份验证信息。

  Example: `'user:pass@host.com:8080'`

  例如：`'user:pass@host.com:8080'`
* `auth`: The authentication information portion of a URL.

  `auth`：URL中的用户身份验证信息。

  Example: `'user:pass'`

  例如：`'user:pass'`
* `hostname`: Just the hostname portion of the host.

  `hostname`：主机信息中的主机名部分。

  Example: `'host.com'`

  例如：`'host.com'`
* `port`: The port number portion of the host.

  `port`：主机信息中的端口部分。

  Example: `'8080'`

  例如：`'8080'`
* `pathname`: The path section of the URL, that comes after the host and before the query, including the initial slash if present.

  `pathname`：URL中的路径部分，这部分信息位于主机信息之后查询字符串之前。如果存在最上层根目录符号`'/'`，也将包含在此信息中。

  Example: `'/p/a/t/h'`

  例如：`'/p/a/t/h'`
* `search`: The 'query string' portion of the URL, including the leading question mark.

  `search`：URL中的'query string'（查询字符串）部分，包括前导的`'?'`。

  Example: `'?query=string'`

  例如：`'?query=string'`
* `query`: Either the 'params' portion of the query string, or a querystring-parsed object.

  `query`：查询字符串中的参数部分，或者是由查询字符串解析出的对象。

  Example: `'query=string'` or `{'query':'string'}`

  例如：`'query=string'` or `{'query':'string'}`
* `hash`: The 'fragment' portion of the URL including the pound-sign.

  `hash`：URL中的锚点部分，包含前导的`'#'`。

  Example: `'#hash'`

  例如：`'#hash'`

The following methods are provided by the URL module:

URL模块提供了如下方法：

### url.parse(urlStr, parseQueryString=false)

Take a URL string, and return an object.  Pass `true` as the second argument to also parse
the query string using the `querystring` module.

以一个 URL字符串为参数，返回一个解析后的对象。如设置第二个参数为`true`，则会使用`querystring`模块解析URL中的查询字符串。

### url.format(urlObj)

Take a parsed URL object, and return a formatted URL string.

以一个解析后的URL对象为参数，返回格式化的URL字符串。

### url.resolve(from, to)

Take a base URL, and a href URL, and resolve them as a browser would for an anchor tag.

指定一个默认URL地址，和一个链接的目标URL地址，返回链接的绝对URL地址。处理方式与浏览器处理锚点标签的方法一致。
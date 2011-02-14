## URL 统一资源定位符模块

This module has utilities for URL resolution and parsing.
Call `require('url')` to use it.

此模块包含用于解析和分析URL的工具。
可通过调用`require('url')`访问他们。

Parsed URL objects have some or all of the following fields, depending on
whether or not they exist in the URL string. Any parts that are not in the URL
string will not be in the parsed object. Examples are shown for the URL

解析后的URL对象包含下述部分或全部字段。具体包含哪些字段取决于解析前
的URL字符串中是否存在这些字段。在原始的URL字符串中不存在的字段在解
析后的对象中也不会包含。例如下面这个URL：

`'http://user:pass@host.com:8080/p/a/t/h?query=string#hash'`

* `href`: The full URL that was originally parsed.

  Example: `'http://user:pass@host.com:8080/p/a/t/h?query=string#hash'`
* `protocol`: The request protocol.

  Example: `'http:'`
* `host`: The full host portion of the URL, including port and authentication information.

  Example: `'user:pass@host.com:8080'`
* `auth`: The authentication information portion of a URL.

  Example: `'user:pass'`
* `hostname`: Just the hostname portion of the host.

  Example: `'host.com'`
* `port`: The port number portion of the host.

  Example: `'8080'`
* `pathname`: The path section of the URL, that comes after the host and before the query, including the initial slash if present.

  Example: `'/p/a/t/h'`
* `search`: The 'query string' portion of the URL, including the leading question mark.

  Example: `'?query=string'`
* `query`: Either the 'params' portion of the query string, or a querystring-parsed object.

  Example: `'query=string'` or `{'query':'string'}`
* `hash`: The 'fragment' portion of the URL including the pound-sign.

  Example: `'#hash'`

The following methods are provided by the URL module:

### url.parse(urlStr, parseQueryString=false)

Take a URL string, and return an object.  Pass `true` as the second argument to also parse
the query string using the `querystring` module.

### url.format(urlObj)

Take a parsed URL object, and return a formatted URL string.

### url.resolve(from, to)

Take a base URL, and a href URL, and resolve them as a browser would for an anchor tag.

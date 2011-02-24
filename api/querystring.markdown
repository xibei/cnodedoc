## Query String

This module provides utilities for dealing with query strings.
It provides the following methods:

## 查询字符串（Query String ）

该模块提为处理查询字符串提供了一些实用的功能。
该模块提供了如下的方法：

### querystring.stringify(obj, sep='&', eq='=')

Serialize an object to a query string.
Optionally override the default separator and assignment characters.

Example:

    querystring.stringify({foo: 'bar'})
    // returns
    'foo=bar'

    querystring.stringify({foo: 'bar', baz: 'bob'}, ';', ':')
    // returns
    'foo:bar;baz:bob'

### querystring.stringify(obj, sep='&', eq='=')


### querystring.parse(str, sep='&', eq='=')

Deserialize a query string to an object.
Optionally override the default separator and assignment characters.

Example:

    querystring.parse('a=b&b=c')
    // returns
    { a: 'b', b: 'c' }

### querystring.escape

The escape function used by `querystring.stringify`,
provided so that it could be overridden if necessary.

### querystring.unescape

The unescape function used by `querystring.parse`,
provided so that it could be overridden if necessary.

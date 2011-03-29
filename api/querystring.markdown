## Query String 查询字符串模块

This module provides utilities for dealing with query strings.
It provides the following methods:

该模块为处理查询字符串提供了一些实用的功能。包括如下的方法：

### querystring.stringify(obj, sep='&', eq='=')

Serialize an object to a query string.
Optionally override the default separator and assignment characters.

将一个对象序列化为一个查询字符串，可选择是否覆盖默认的分隔符和赋值符。

Example:

例如：

    querystring.stringify({foo: 'bar'})
    // returns
    'foo=bar'

    querystring.stringify({foo: 'bar', baz: 'bob'}, ';', ':')
    // returns
    'foo:bar;baz:bob'

### querystring.parse(str, sep='&', eq='=')

Deserialize a query string to an object.
Optionally override the default separator and assignment characters.

将一个查询字符串反序列化为一个对象，可选择是否覆盖默认的分隔符和赋值符。

Example:

例如：

    querystring.parse('a=b&b=c')
    // returns
    { a: 'b', b: 'c' }

### querystring.escape

The escape function used by `querystring.stringify`,
provided so that it could be overridden if necessary.

由`querystring.stringify`使用的转义函数，需要时可重置其内容。

### querystring.unescape

The unescape function used by `querystring.parse`,
provided so that it could be overridden if necessary.

由`querystring.parse`使用的反转义函数，需要时可重置其内容。

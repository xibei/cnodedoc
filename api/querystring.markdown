## Query String

This module provides utilities for dealing with query strings.
It provides the following methods:

## 查询字符串（Query String ）

该模块提为处理查询字符串提供了一些实用的功能。
该模块提供了如下的方法：

### querystring.stringify(obj, sep='&', eq='=')

Serialize an object to a query string.

序列化一个对象为一个查询字符串。
Optionally override the default separator and assignment characters.

可以有选择地地重载默认的操作符与分配字符

Example:

    querystring.stringify({foo: 'bar'})
    // returns
    'foo=bar'

    querystring.stringify({foo: 'bar', baz: 'bob'}, ';', ':')
    // returns
    'foo:bar;baz:bob'

### querystring.parse(str, sep='&', eq='=')

Deserialize a query string to an object.

将一个查询字符串转化成一个对象

Optionally override the default separator and assignment characters.

可以有选择地重载其默认分隔符与分配字符。

Example:

    querystring.parse('a=b&b=c')
    // returns
    { a: 'b', b: 'c' }

### querystring.escape

The escape function used by `querystring.stringify`,

这个取消函数由`querystring.stringify`使用，

provided so that it could be overridden if necessary.

提供这个函数一个目的是为了可以在必要的时候重载它。

### querystring.unescape

The unescape function used by `querystring.parse`,

非取消函数由`querystring.parse`使用，

provided so that it could be overridden if necessary.

提供这个函数一个目的是为了可以在必要的时候重载它。

## Buffers
##缓存器

Pure Javascript is Unicode friendly but not nice to binary data.  When
dealing with TCP streams or the file system, it's necessary to handle octet
streams. Node has several strategies for manipulating, creating, and
consuming octet streams.
纯Javascript语言是Unicode友好性的，但是难以表述成二进制代码。在处理TCP流和文件系统时经常需要操作字节流。Node提供了一些列机制，用于操作、创建、以及消耗（consuming）字节流。

Raw data is stored in instances of the `Buffer` class. A `Buffer` is similar
to an array of integers but corresponds to a raw memory allocation outside
the V8 heap. A `Buffer` cannot be resized.
在实例化的`Buffer`类中存储了原始数据。相比整数数组，`Buffer`对应了在V8堆（the V8 heap）外的原始存储空间分配。但是，一旦创建了`Buffer`实例，其存储空间无法改变。

The `Buffer` object is global.
另外，`Buffer`是一个全局对象。

Converting between Buffers and JavaScript string objects requires an explicit encoding
method.  Here are the different string encodings;
在缓冲器（Buffers）和JavaScript间进行字符串的转换需要调用特定的编码方法。如下列举了不同的编码方法：

* `'ascii'` - for 7 bit ASCII data only.  This encoding method is very fast, and will
strip the high bit if set.
* `'ascii'` - 仅对应7为的ASCII数据。虽然这种编码方式非常迅速，但是采用此编码方式需要大量存储空间。

* `'utf8'` - Unicode characters.  Many web pages and other document formats use UTF-8.
* `'utf8'` - 对应Unicode字符。大量网页和其他文件格式使用这类编码方式。

* `'base64'` - Base64 string encoding.
* `'base64'` - Base64 字符串编码.

* `'binary'` - A way of encoding raw binary data into strings by using only
the first 8 bits of each character. This encoding method is depreciated and
should be avoided in favor of `Buffer` objects where possible. This encoding
will be removed in future versions of Node.
* `'binary'` - 仅使用每个字符的头8位将原始的二进制信息进行编码。但是这种编码方式已经过时了，在使用`Buffer`时应该尽量避免使用这个编码方式。而且，这个编码方式不会出现在未来版本的Node中。

### new Buffer(size)

Allocates a new buffer of `size` octets.
分配给一个新创建的buffer实例一个大小为`size`字节的空间。

### new Buffer(array)

Allocates a new buffer using an `array` of octets.
使用`array`的空间创建一个buffer实例。

### new Buffer(str, encoding='utf8')

Allocates a new buffer containing the given `str`.
创建一个包含给定`str`的buffer实例。

### buffer.write(string, offset=0, encoding='utf8')

Writes `string` to the buffer at `offset` using the given encoding. Returns
number of octets written.  If `buffer` did not contain enough space to fit
the entire string, it will write a partial amount of the string. In the case
of `'utf8'` encoding, the method will not write partial characters.

Example: write a utf8 string into a buffer, then print it

    buf = new Buffer(256);
    len = buf.write('\u00bd + \u00bc = \u00be', 0);
    console.log(len + " bytes: " + buf.toString('utf8', 0, len));

    // 12 bytes: ½ + ¼ = ¾


### buffer.toString(encoding, start=0, end=buffer.length)

Decodes and returns a string from buffer data encoded with `encoding`
beginning at `start` and ending at `end`.

See `buffer.write()` example, above.


### buffer[index]

Get and set the octet at `index`. The values refer to individual bytes,
so the legal range is between `0x00` and `0xFF` hex or `0` and `255`.

Example: copy an ASCII string into a buffer, one byte at a time:

    str = "node.js";
    buf = new Buffer(str.length);

    for (var i = 0; i < str.length ; i++) {
      buf[i] = str.charCodeAt(i);
    }

    console.log(buf);

    // node.js

### Buffer.isBuffer(obj)

Tests if `obj` is a `Buffer`.

### Buffer.byteLength(string, encoding='utf8')

Gives the actual byte length of a string.  This is not the same as
`String.prototype.length` since that returns the number of *characters* in a
string.

Example:

    str = '\u00bd + \u00bc = \u00be';

    console.log(str + ": " + str.length + " characters, " +
      Buffer.byteLength(str, 'utf8') + " bytes");

    // ½ + ¼ = ¾: 9 characters, 12 bytes


### buffer.length

The size of the buffer in bytes.  Note that this is not necessarily the size
of the contents. `length` refers to the amount of memory allocated for the
buffer object.  It does not change when the contents of the buffer are changed.

    buf = new Buffer(1234);

    console.log(buf.length);
    buf.write("some string", "ascii", 0);
    console.log(buf.length);

    // 1234
    // 1234

### buffer.copy(targetBuffer, targetStart=0, sourceStart=0, sourceEnd=buffer.length)

Does a memcpy() between buffers.

Example: build two Buffers, then copy `buf1` from byte 16 through byte 19
into `buf2`, starting at the 8th byte in `buf2`.

    buf1 = new Buffer(26);
    buf2 = new Buffer(26);

    for (var i = 0 ; i < 26 ; i++) {
      buf1[i] = i + 97; // 97 is ASCII a
      buf2[i] = 33; // ASCII !
    }

    buf1.copy(buf2, 8, 16, 20);
    console.log(buf2.toString('ascii', 0, 25));

    // !!!!!!!!qrst!!!!!!!!!!!!!


### buffer.slice(start, end=buffer.length)

Returns a new buffer which references the
same memory as the old, but offset and cropped by the `start` and `end`
indexes.

**Modifying the new buffer slice will modify memory in the original buffer!**

Example: build a Buffer with the ASCII alphabet, take a slice, then modify one byte
from the original Buffer.

    var buf1 = new Buffer(26);

    for (var i = 0 ; i < 26 ; i++) {
      buf1[i] = i + 97; // 97 is ASCII a
    }

    var buf2 = buf1.slice(0, 3);
    console.log(buf2.toString('ascii', 0, buf2.length));
    buf1[0] = 33;
    console.log(buf2.toString('ascii', 0, buf2.length));

    // abc
    // !bc

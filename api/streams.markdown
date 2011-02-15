## Streams 流

A stream is an abstract interface implemented by various objects in Node.
For example a request to an HTTP server is a stream, as is stdout. Streams
are readable, writable, or both. All streams are instances of `EventEmitter`.

在Node中，流是一个由各种对象实现的抽象接口。例如请求HTTP服务器的request是一个流，类似于标准输出。流可以是可读的，可写的，或者既可读又可写。所有流都是`EventEmitter`的实例。

## Readable Stream 可读流

A `Readable Stream` has the following methods, members, and events.

一个`可读的流`具有下述的方法、成员、及事件。

### Event: 'data' 事件：'data'

`function (data) { }`

The `'data'` event emits either a `Buffer` (by default) or a string if
`setEncoding()` was used.

`'data'`事件的参数默认情况下是一个`Buffer`缓冲区对象。如果使用了`setEncoding()` 则参数为一个字符串。

### Event: 'end' 事件：'end'

`function () { }`

Emitted when the stream has received an EOF (FIN in TCP terminology).
Indicates that no more `'data'` events will happen. If the stream is also
writable, it may be possible to continue writing.

当流中接收到EOF（TCP中为FIN）时此事件被触发，表示不会再发生任何`'data'`事件。如果流同时也是可写的，那它还可以继续写入。

### Event: 'error' 事件：'error'

`function (exception) { }`

Emitted if there was an error receiving data.

接收数据的过程中发生任何错误时，此事件被触发。

### Event: 'close' 事件：'close'

`function () { }`

Emitted when the underlying file descriptor has been closed. Not all streams
will emit this.  (For example, an incoming HTTP request will not emit
`'close'`.)

当底层的文件描述符被关闭时触发此事件，并不是所有流都会触发这个事件。（例如，一个连接进来的HTTP请求就不会触发`'close'`事件。）

### Event: 'fd' 事件：'fd'

`function (fd) { }`

Emitted when a file descriptor is received on the stream. Only UNIX streams
support this functionality; all others will simply never emit this event.

当在流中接收到一个文件描述符时触发此事件。只有UNIX流支持这个功能，其他类型的流均不会触发此事件。

### stream.readable

A boolean that is `true` by default, but turns `false` after an `'error'`
occured, the stream came to an `'end'`, or `destroy()` was called.

这是一个布尔值，默认值为`true`。当`'error'`事件、`'end'`事件发生后，或者`destroy()`被调用后，这个属性将变为`false`。

### stream.setEncoding(encoding)
Makes the data event emit a string instead of a `Buffer`. `encoding` can be
`'utf8'`, `'ascii'`, or `'base64'`.

调用此方法会影响`'data'`事件的回调函数参数。默认参数为`Buffer`缓冲区对象，调用此方法后参数为一个字符串。`encoding`参数可以是`'utf8'`、`'ascii'`、或`'base64'`。

### stream.pause()

Pauses the incoming `'data'` events.

暂停`'data'`事件的触发。

### stream.resume()

Resumes the incoming `'data'` events after a `pause()`.

恢复被`pause()`调用暂停的`'data'`事件触发。

### stream.destroy()

Closes the underlying file descriptor. Stream will not emit any more events.

关闭底层的文件描述符。流将不会再触发任何事件。


### stream.destroySoon()

After the write queue is drained, close the file descriptor.

在写队列清空后（所有写操作完成后），关闭文件描述符。

### stream.pipe(destination, [options])

This is a `Stream.prototype` method available on all `Stream`s.

这是`Stream.prototype`（Stream原型对象）的一个方法，对所有`Stream`流对象有效。

Connects this read stream to `destination` WriteStream. Incoming
data on this stream gets written to `destination`. The destination and source
streams are kept in sync by pausing and resuming as necessary.

用于将这个可读流和`destination`目标可写流连接起来，传入这个流中的数据将会写入到`destination`流中。通过在必要时暂停和恢复流，来源流和目的流得以保持同步。

Emulating the Unix `cat` command:

模拟Unix系统的`cat`命令：

    process.stdin.resume();
    process.stdin.pipe(process.stdout);


By default `end()` is called on the destination when the source stream emits
`end`, so that `destination` is no longer writable. Pass `{ end: false }` as
`options` to keep the destination stream open.

默认情况下，当来源流的`end`事件触发时目的流的`end()`方法会被调用，此时`destination`目的流将不再可写入。要在这种情况下保持目的流仍然可写入，可将`options`参数设为`{ end: false }`。

This keeps `process.stdout` open so that "Goodbye" can be written at the end.

这使`process.stdout`保持打开状态，因此"Goodbye"可以在end事件发生后被写入。

    process.stdin.resume();

    process.stdin.pipe(process.stdout, { end: false });

    process.stdin.on("end", function() {
      process.stdout.write("Goodbye\n");
    });

NOTE: If the source stream does not support `pause()` and `resume()`, this function
adds simple definitions which simply emit `'pause'` and `'resume'` events on
the source stream.

注意：如果来源流不支持`pause()`和`resume()`方法，此函数将在来源流对象上增加这两个方法的简单的定义，内容为触发`'pause'`和`'resume'`事件。

## Writable Stream 可写流

A `Writable Stream` has the following methods, members, and events.

一个`可写流`具有下列方法、成员、和事件。

### Event: 'drain' 事件：'drain'

`function () { }`

Emitted after a `write()` method was called that returned `false` to
indicate that it is safe to write again.

发生在`write()`方法被调用并返回`false`之后。此事件被触发说明内核缓冲区已空，再次写入是安全的。

### Event: 'error' 事件：'error'

`function (exception) { }`

Emitted on error with the exception `exception`.

发生错误时被触发，回调函数接收一个异常参数`exception`。

### Event: 'close' 事件：'close'

`function () { }`

Emitted when the underlying file descriptor has been closed.

底层文件描述符被关闭时被触发。

### Event: 'pipe' 事件：'pipe'

`function (src) { }`

Emitted when the stream is passed to a readable stream's pipe method.

当此可写流作为参数传给一个可读流的pipe方法时被触发。

### stream.writable

A boolean that is `true` by default, but turns `false` after an `'error'`
occurred or `end()` / `destroy()` was called.

一个布尔值，默认值为`true`。在`'error'`事件被触发之后，或`end()` / `destroy()`方法被调用后此属性被设为`false`。

### stream.write(string, encoding='utf8', [fd])

Writes `string` with the given `encoding` to the stream.  Returns `true` if
the string has been flushed to the kernel buffer.  Returns `false` to
indicate that the kernel buffer is full, and the data will be sent out in
the future. The `'drain'` event will indicate when the kernel buffer is
empty again. The `encoding` defaults to `'utf8'`.

使用指定编码`encoding`将字符串`string`写入到流中。如果字符串被成功写入内核缓冲区，此方法返回`true`。如果内核缓冲区已满，此方法返回`false`，数据将在以后被送出。当内核缓冲区再次被清空后'drain'`事件将被触发。`encoding`参数默认为`'utf8'`。

If the optional `fd` parameter is specified, it is interpreted as an integral
file descriptor to be sent over the stream. This is only supported for UNIX
streams, and is silently ignored otherwise. When writing a file descriptor in
this manner, closing the descriptor before the stream drains risks sending an
invalid (closed) FD.

如果指定了可选参数`fd`，它将被作为一个文件描述符通过流传送。此功能仅被Unix流所支持，对于其他流此操作将被忽略而没有任何提示。当使用此方法传送一个文件描述符时，如果在流没有清空前关闭此文件描述符，将造成传送一个无效（已关闭）FD的风险。

### stream.write(buffer)

Same as the above except with a raw buffer.

除了用一个原始的缓冲区对象替代字符串之外，其他同上。

### stream.end()

Terminates the stream with EOF or FIN.

使用EOF或FIN结束一个流的输出。

### stream.end(string, encoding)

Sends `string` with the given `encoding` and terminates the stream with EOF
or FIN. This is useful to reduce the number of packets sent.

以指定的字符编码`encoding`传送一个字符串`string`，然后使用EOF或FIN结束流的输出。这对降低数据包传输量有所帮助。

### stream.end(buffer)

Same as above but with a `buffer`.

除了用一个缓冲区对象`buffer`替代字符串之外，其他同上。

### stream.destroy()

Closes the underlying file descriptor. Stream will not emit any more events.

关闭底层文件描述符。在此流上将不会再触发任何事件。


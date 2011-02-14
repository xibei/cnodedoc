## util 工具模块

These functions are in the module `'util'`. Use `require('util')` to access
them.

下列函数属于`'工具'`模块，可通过调用`require('util')`访问它们。


### util.debug(string)

A synchronous output function. Will block the process and
output `string` immediately to `stderr`.

这是一个同步输出函数，将`string`参数的内容实时输出到`标准错误`。调用此函数时将阻塞当前进程直到输出完成。

    require('util').debug('message on stderr');


### util.log(string)

Output with timestamp on `stdout`.

将`string`参数的内容输出到`标准输出`，并加上当前时间戳。

    require('util').log('Timestmaped message.');


### util.inspect(object, showHidden=false, depth=2)

Return a string representation of `object`, which is useful for debugging.

以字符串形式返回`object`对象的结构信息，这对程序调试非常有帮助。

If `showHidden` is `true`, then the object's non-enumerable properties will be
shown too.

如果`showHidden`参数设置为`true`，则此对象的非枚举属性也会被显示。


If `depth` is provided, it tells `inspect` how many times to recurse while
formatting the object. This is useful for inspecting large complicated objects.

The default is to only recurse twice.  To make it recurse indefinitely, pass
in `null` for `depth`.

Example of inspecting all properties of the `util` object:

    var util = require('util');

    console.log(util.inspect(util, true, null));


### util.pump(readableStream, writableStream, [callback])

Experimental

Read the data from `readableStream` and send it to the `writableStream`.
When `writableStream.write(data)` returns `false` `readableStream` will be
paused until the `drain` event occurs on the `writableStream`. `callback` gets
an error as its only argument and is called when `writableStream` is closed or
when an error occurs.


### util.inherits(constructor, superConstructor)

Inherit the prototype methods from one
[constructor](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Object/constructor)
into another.  The prototype of `constructor` will be set to a new
object created from `superConstructor`.

As an additional convenience, `superConstructor` will be accessible
through the `constructor.super_` property.

    var util = require("util");
    var events = require("events");

    function MyStream() {
        events.EventEmitter.call(this);
    }

    util.inherits(MyStream, events.EventEmitter);

    MyStream.prototype.write = function(data) {
        this.emit("data", data);
    }

    var stream = new MyStream();

    console.log(stream instanceof events.EventEmitter); // true
    console.log(MyStream.super_ === events.EventEmitter); // true

    stream.on("data", function(data) {
        console.log('Received data: "' + data + '"');
    })
    stream.write("It works!"); // Received data: "It works!"

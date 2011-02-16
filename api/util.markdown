## util 工具模块

These functions are in the module `'util'`. Use `require('util')` to access
them.

下列函数属于`'util'`（工具）模块，可使用`require('util')`访问它们。


### util.debug(string)

A synchronous output function. Will block the process and
output `string` immediately to `stderr`.

这是一个同步输出函数，将`string`参数的内容实时输出到`stderr`标准错误。调用此函数时将阻塞当前进程直到输出完成。

    require('util').debug('message on stderr');


### util.log(string)

Output with timestamp on `stdout`.

将`string`参数的内容加上当前时间戳，输出到`stdout`标准输出。

    require('util').log('Timestmaped message.');


### util.inspect(object, showHidden=false, depth=2)

Return a string representation of `object`, which is useful for debugging.

以字符串形式返回`object`对象的结构信息，这对程序调试非常有帮助。

If `showHidden` is `true`, then the object's non-enumerable properties will be
shown too.

如果`showHidden`参数设置为`true`，则此对象的不可枚举属性也会被显示。

If `depth` is provided, it tells `inspect` how many times to recurse while
formatting the object. This is useful for inspecting large complicated objects.

可使用`depth`参数指定`inspect`函数在格式化对象信息时的递归次数。这对分析复杂对象的内部结构非常有帮助。

The default is to only recurse twice.  To make it recurse indefinitely, pass
in `null` for `depth`.

默认情况下递归两次，如果想要无限递归可将`depth`参数设为`null`。

Example of inspecting all properties of the `util` object:

显示`util`对象所有属性的例子如下：

    var util = require('util');

    console.log(util.inspect(util, true, null));


### util.pump(readableStream, writableStream, [callback])

Experimental

实验性的

Read the data from `readableStream` and send it to the `writableStream`.
When `writableStream.write(data)` returns `false` `readableStream` will be
paused until the `drain` event occurs on the `writableStream`. `callback` gets
an error as its only argument and is called when `writableStream` is closed or
when an error occurs.

从`readableStream`参数所指定的可读流中读取数据，并将其写入到`writableStream`参数所指定的可写流中。当`writeableStream.write(data)`函数调用返回为`false`时，`readableStream`流将被暂停，直到在`writableStream`流上发生`drain`事件。当`writableStream`流被关闭或发生一个错误时，`callback`回调函数被调用。此回调函数只接受一个参数用以指明所发生的错误。


### util.inherits(constructor, superConstructor)

Inherit the prototype methods from one
[constructor](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Object/constructor)
into another.  The prototype of `constructor` will be set to a new
object created from `superConstructor`.

将一个[构造函数](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Object/constructor)的原型方法继承到另一个构造函数中。`constructor`构造函数的原型将被设置为使用`superConstructor`构造函数所创建的一个新对象。

As an additional convenience, `superConstructor` will be accessible
through the `constructor.super_` property.

此方法带来的额外的好处是，可以通过`constructor.super_`属性来访问`superConstructor`构造函数。

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

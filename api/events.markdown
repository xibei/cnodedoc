## Events 事件模块

Many objects in Node emit events: a `net.Server` emits an event each time
a peer connects to it, a `fs.readStream` emits an event when the file is
opened. All objects which emit events are instances of `events.EventEmitter`.
You can access this module by doing: `require("events");`

Node引擎中很多对象都会触发事件：例如`net.Server`会在每一次有客户端连接到它时触发事件，又如`fs.readStream`会在文件打开时触发事件。所有能够触发事件的对象都是`events.EventEmitter`的实例。你可以通过`require("events");`访问这个模块。

Typically, event names are represented by a camel-cased string, however,
there aren't any strict restrictions on that, as any string will be accepted.

通常情况下，事件名称采用驼峰式写法，不过目前并没有对事件名称作任何的限制，也就是说任何的字符串都可以被接受。

Functions can then be attached to objects, to be executed when an event
is emitted. These functions are called _listeners_.

可以将函数注册给对象，使其在事件触发时执行，此类函数被称作_监听器_。

### events.EventEmitter

To access the EventEmitter class, `require('events').EventEmitter`.

通过调用`require('events').EventEmitter`，我们可以使用事件触发器类。 

When an `EventEmitter` instance experiences an error, the typical action is
to emit an `'error'` event.  Error events are treated as a special case in node.
If there is no listener for it, then the default action is to print a stack
trace and exit the program.

当`EventEmitter`事件触发器遇到错误时，典型的处理方式是它将触发一个`'error'`事件。Error事件的特殊性在于：如果没有函数处理这个事件，它将会输出调用堆栈，并随之退出应用程序。

All EventEmitters emit the event `'newListener'` when new listeners are
added.

当新的事件监听器被添加时，所有的事件触发器都将触发名为`'newListener'`的事件。

#### emitter.addListener(event, listener)
#### emitter.on(event, listener)

Adds a listener to the end of the listeners array for the specified event.

将一个监听器添加到指定事件的监听器数组的末尾。

    server.on('connection', function (stream) {
      console.log('someone connected!');
    });

#### emitter.once(event, listener)

Adds a **one time** listener for the event. The listener is
invoked only the first time the event is fired, after which
it is removed.

为事件添加**一次性**的监听器。该监听器在事件第一次触发时执行，过后将被移除。

    server.once('connection', function (stream) {
      console.log('Ah, we have our first user!');
    });

#### emitter.removeListener(event, listener)

Remove a listener from the listener array for the specified event.
**Caution**: changes array indices in the listener array behind the listener.

将监听器从指定事件的监听器数组中移除出去。
**小心**：此操作将改变监听器数组的下标。

    var callback = function(stream) {
      console.log('someone connected!');
    };
    server.on('connection', callback);
    // ...
    server.removeListener('connection', callback);


#### emitter.removeAllListeners(event)

Removes all listeners from the listener array for the specified event.

将指定事件的所有监听器从监听器数组中移除。

#### emitter.setMaxListeners(n)

By default EventEmitters will print a warning if more than 10 listeners are
added to it. This is a useful default which helps finding memory leaks.
Obviously not all Emitters should be limited to 10. This function allows
that to be increased. Set to zero for unlimited.

默认情况下当事件触发器注册了超过10个以上的监听器时系统会打印警告信息，这个默认配置将有助于你查找内存泄露问题。很显然并不是所有的事件触发器都需要进行10个监听器的限制，此函数允许你手动设置该数量值，如果值为0意味值没有限制。

#### emitter.listeners(event)

Returns an array of listeners for the specified event. This array can be
manipulated, e.g. to remove listeners.

返回指定事件的监听器数组对象，你可以对该数组进行操作，比如说删除监听器等。

    server.on('connection', function (stream) {
      console.log('someone connected!');
    });
    console.log(util.inspect(server.listeners('connection')); // [ [Function] ]

#### emitter.emit(event, [arg1], [arg2], [...])

Execute each of the listeners in order with the supplied arguments.

根据提供的参数顺序执行监听器列表中的每个监听器函数。

#### Event: 'newListener' 事件：'newListener'

`function (event, listener) { }`

This event is emitted any time someone adds a new listener.

任何时候只要新的监听器被添加时该事件就会触发。

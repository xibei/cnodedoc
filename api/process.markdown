## process 进程

The `process` object is a global object and can be accessed from anywhere.

 `process`对象是一个全局对象，可以在任何地方访问它。

It is an instance of `EventEmitter`.

它是`EventEmitter`类的一个实例

### Event: 'exit'

`function () {}`

Emitted when the process is about to exit.  This is a good hook to perform

当进程对象要退出时会触发此方法。这对于执行

constant time checks of the module's state (like for unit tests).  The main

是定时检查模块状态来说（比如单元测试）一个不错的工具。

event loop will no longer be run after the 'exit' callback finishes, so

当 'exit'被调用完成后主事件循环将终止，所以

timers may not be scheduled.

计时器可能会因此不按设定计时。


Example of listening for `exit`:

监听 `exit`行为的示例：

    process.on('exit', function () {
      process.nextTick(function () {
       console.log('This will not run');
      });
      console.log('About to exit.');
    });



### Event: 'uncaughtException'

`function (err) { }`

Emitted when an exception bubbles all the way back to the event loop. If a

当一个异常信息进入事件循环时，该方法被触发。

listener is added for this exception, the default action (which is to print

如果该异常有一个监听器，那么默认的行为（即打印输出一个堆栈轨迹并退出）

a stack trace and exit) will not occur.

不会发生。

Example of listening for `uncaughtException`:

监听`uncaughtException`事件的示例：

    process.on('uncaughtException', function (err) {
      console.log('Caught exception: ' + err);
    });

    setTimeout(function () {
      console.log('This will still run.');
    }, 500);

    // Intentionally cause an exception, but don't catch it.
    nonexistentFunc();
    console.log('This will not run.');

Note that `uncaughtException` is a very crude mechanism for exception

注意：就异常处理来说， `uncaughtException`是一个很粗糙的机制。

handling.  Using try / catch in your program will give you more control over

在程序中使用  try / catch可以更好好控制程序流。

your program's flow.  Especially for server programs that are designed to

不过在服务器端的编程中，如果这些代码会永久运行，

stay running forever, `uncaughtException` can be a useful safety mechanism.

那么 `uncaughtException`还是一个很有用的安全机制

### Signal Events 信号事件

`function () {}`

Emitted when the processes receives a signal. See sigaction(2) for a list of

该事件会在进程接收到一个信号时被触发。可参见sigaction(2)中的标准

standard POSIX signal names such as SIGINT, SIGUSR1, etc.

POSIX信号名称列表，比如SIGINT，SIGUSR1等等。


Example of listening for `SIGINT`:

监听 `SIGINT`的示例：

    // Start reading from stdin so we don't exit.
    process.stdin.resume();

    process.on('SIGINT', function () {
      console.log('Got SIGINT.  Press Control-D to exit.');
    });

An easy way to send the `SIGINT` signal is with `Control-C` in most terminal
programs.

在大多数终端程序中，一个简易发送 `SIGINT`信号的方法是在使用`Control-C`命令操作。

### process.stdout

A `Writable Stream` to `stdout`.

一个到标准输出流`stdout`的 `Writable Stream`可写流

Example: the definition of `console.log`

示例：定义`console.log`

    console.log = function (d) {
      process.stdout.write(d + '\n');
    };


### process.stderr

A writable stream to stderr. Writes on this stream are blocking.

一个到错误流stderr的可写流，在这个流上的写操作是受阻的。

### process.stderr

process是一个可以与stderr通信的输入输出流，该流中的输出是分块的。

### process.stdin

A `Readable Stream` for stdin. The stdin stream is paused by default, so one

一个到标准输入流的可读流`Readable Stream`。默认情况下标准输入流是受阻的，

must call `process.stdin.resume()` to read from it.

所以需要调用方法`process.stdin.resume()`来从中读取信息。

Example of opening standard input and listening for both events:

示例：打开标准输入与监听：

    process.stdin.resume();
    process.stdin.setEncoding('utf8');

    process.stdin.on('data', function (chunk) {
      process.stdout.write('data: ' + chunk);
    });

    process.stdin.on('end', function () {
      process.stdout.write('end');
    });


### process.stdin


### process.argv

An array containing the command line arguments.  The first element will be
一个包含命令行参数的数组。第一个元素是'node'，

'node', the second element will be the name of the JavaScript file.  The
第二个元素是JavaScript文件的文件名。

next elements will be any additional command line arguments.
接下来的元素则是附加的命令行参数。



    // print process.argv
    process.argv.forEach(function (val, index, array) {
      console.log(index + ': ' + val);
    });

This will generate:

这产生如下的信息：

    $ node process-2.js one two=three four
    0: node
    1: /Users/mjr/work/node/process-2.js
    2: one
    3: two=three
    4: four


### process.execPath

This is the absolute pathname of the executable that started the process.

这是一个启动该进程的可执行程序的绝对路径名。

Example:

    /usr/local/bin/node


### process.chdir(directory)

Changes the current working directory of the process or throws an exception if that fails.

改变进程的当前工作目录，如果操作失败则抛出异常。

    console.log('Starting directory: ' + process.cwd());
    try {
      process.chdir('/tmp');
      console.log('New directory: ' + process.cwd());
    }
    catch (err) {
      console.log('chdir: ' + err);
    }



### process.cwd()

Returns the current working directory of the process.

返回进程的当前工作目录。

    console.log('Current directory: ' + process.cwd());


### process.env

An object containing the user environment. See environ(7).

一个包括用户环境的对象。可参见environ(7)。


### process.exit(code=0)

Ends the process with the specified `code`.  If omitted, exit uses the
'success' code `0`.

用指定的参数`code`代码结束进程。如果不指定，退出时将使用'success' 代码 `0`。

To exit with a 'failure' code:

以'failure' 代码退出的示例：

    process.exit(1);

The shell that executed node should see the exit code as 1.

行该node的shell会把退出代码视为1。


### process.getgid()

Gets the group identity of the process. (See getgid(2).)
This is the numerical group id, not the group name.

获取进程的群组标识（详见getgid(2)）。这是一个数字群组ID，
不是群组名称。

    console.log('Current gid: ' + process.getgid());


### process.setgid(id)

Sets the group identity of the process. (See setgid(2).)  This accepts either

设置进程的群组标识（详见getgid(2)）。这会接受
a numerical ID or a groupname string. If a groupname is specified, this method

一个数字ID或者一个群组名字符串。如果指定了一个群组名，那么当系统要将

blocks while resolving it to a numerical ID.

其解析成一个数字ID时，该方法会阻此这种操作。




    console.log('Current gid: ' + process.getgid());
    try {
      process.setgid(501);
      console.log('New gid: ' + process.getgid());
    }
    catch (err) {
      console.log('Failed to set gid: ' + err);
    }


### process.getuid()

Gets the user identity of the process. (See getuid(2).)

获取用户的进程ID详见getgid(2)）。

This is the numerical userid, not the username.

这是一个数字用户ID，不是用户名。

    console.log('Current uid: ' + process.getuid());


### process.setuid(id)

Sets the user identity of the process. (See setuid(2).)  This accepts either

设置进程的用户标识详见getgid(2)）。该方法接受

a numerical ID or a username string.  If a username is specified, this method

一个数字ID或者一个用户名字符串。如果用户名已指定，那么该方法

blocks while resolving it to a numerical ID.

会阻止系统将其解析成一个用户名ID。


    console.log('Current uid: ' + process.getuid());
    try {
      process.setuid(501);
      console.log('New uid: ' + process.getuid());
    }
    catch (err) {
      console.log('Failed to set uid: ' + err);
    }


### process.version

A compiled-in property that exposes `NODE_VERSION`.

一个内置的属性，用于显示`NODE_VERSION`。

    console.log('Version: ' + process.version);

### process.installPrefix

A compiled-in property that exposes `NODE_PREFIX`.

一个内置的属性，用于显示`NODE_PREFIX`

    console.log('Prefix: ' + process.installPrefix);


### process.kill(pid, signal='SIGTERM')

Send a signal to a process. `pid` is the process id and `signal` is the

发送一个信号到进程。`pid`是进程的ID，参数`signal`是

string describing the signal to send.  Signal names are strings like

的字符串。信号名称是像'SIGINT' 或者 'SIGUSR1

'SIGINT' or 'SIGUSR1'.  If omitted, the signal will be 'SIGTERM'.

这样的字符串。如果参数`signal`忽略，则信号为 'SIGTERM'。

See kill(2) for more information.

详见kill(2)。


Note that just because the name of this function is `process.kill`, it is

注意刚好由于该函数名为`process.kill`,它

really just a signal sender, like the `kill` system call.  The signal sent

实际上也仅仅是一个信号发送器，就像 `kill`系统调用一样。这个被发送的信号

may do something other than kill the target process.

除了终止目标进程外，可能还会有其它的功能。

Example of sending a signal to yourself:

一个给自己发送一个信号的示例：

    process.on('SIGHUP', function () {
      console.log('Got SIGHUP signal.');
    });

    setTimeout(function () {
      console.log('Exiting.');
      process.exit(0);
    }, 100);

    process.kill(process.pid, 'SIGHUP');


### process.pid

The PID of the process.

进程的PID。

    console.log('This process is pid ' + process.pid);

### process.title

Getter/setter to set what is displayed in 'ps'.

设置在 'ps'显示的内容的Getter/setter方法。


### process.platform

What platform you're running on. `'linux2'`, `'darwin'`, etc.

记录你运行Node的平台信息，如'linux2'`、 `'darwin'`等等。

    console.log('This platform is ' + process.platform);


### process.memoryUsage()

Returns an object describing the memory usage of the Node process.

返回一个对象来描述Node进程的内存使用情况。

    var util = require('util');

    console.log(util.inspect(process.memoryUsage()));

This will generate:

这会生成如下信息：

    { rss: 4935680,
      vsize: 41893888,
      heapTotal: 1826816,
      heapUsed: 650472 }

`heapTotal` and `heapUsed` refer to V8's memory usage.

`heapTotal`与`heapUsed`指 V8的内存使用情况。


### process.nextTick(callback)

On the next loop around the event loop call this callback.
This is *not* a simple alias to `setTimeout(fn, 0)`, it's much more
efficient.

在事件循环的下一个循环调用这个方法。这*不是*`setTimeout(fn, 0)`
的一个别名，因为它有效率多了。

    process.nextTick(function () {
      console.log('nextTick callback');
    });


### process.umask([mask])

Sets or reads the process's file mode creation mask. Child processes inherit

设置或者读取进程的文件模式创建特征码。子进程从父进程中继承

the mask from the parent process. Returns the old mask if `mask` argument is

这个特征码。如果参数 `mask`设定了，那么返回旧的特征码

given, otherwise returns the current mask.

否则返回当前的。


    var oldmask, newmask = 0644;

    oldmask = process.umask(newmask);
    console.log('Changed umask from: ' + oldmask.toString(8) +
                ' to ' + newmask.toString(8));


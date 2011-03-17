## REPL  读值-求值-打印循环

A Read-Eval-Print-Loop (REPL) is available both as a standalone program and easily
includable in other programs.  REPL provides a way to interactively run
JavaScript and see the results.  It can be used for debugging, testing, or
just trying things out.


读值-求值-打印循环（以下简称REPL）既可以以一个独立的程序运行，也可以很容易地
包含在其他程序中作为整体程序的一部分使用。REPL为Node.js运行JavaScript脚本与查看运行
结果提供了一种交互方式，通常REPL交互方式可以用于调试、测试以及试验某种想法。


By executing `node` without any arguments from the command-line you will be
dropped into the REPL. It has simplistic emacs line-editing.


在命令行中不带任何参数地运行`node`命令，就可以进入REPL环境，在该环境下
你可以进行一些类似Emacs的命令行编辑操作。

    mjr:~$ node
    Type '.help' for options.
    > a = [ 1, 2, 3];
    [ 1, 2, 3 ]
    > a.forEach(function (v) {
    ...   console.log(v);
    ...   });
    1
    2
    3

For advanced line-editors, start node with the environmental variable `NODE_NO_READLINE=1`.
This will start the REPL in canonical terminal settings which will allow you to use with `rlwrap`.

对于高级的命令行编辑操作，可以令环境变量`NODE_NO_READLINE=1`的方式启动node。
这种情况REPL会进入常见的终端设置模式，这该模式下你可以使用`rlwrap`进行操作。

For example, you could add this to your bashrc file:

比如，你可以把下列设置添加到你的bashrc文件中：

    alias node="env NODE_NO_READLINE=1 rlwrap node"

## REPL 读值-求值-打印循环


### repl.start(prompt='> ', stream=process.stdin)

Starts a REPL with `prompt` as the prompt and `stream` for all I/O.  `prompt`
is optional and defaults to `> `.  `stream` is optional and defaults to
`process.stdin`.


该方法是可以设置REPL的命令提示符类型与I/O流的方式。具体说，参数 `prompt`用于设置
命令行提示符，其值可选，默认值为 `> `，而参数`stream`设置所有的 I/O流，其可选，其值
可选，默认值为 `process.stdin`。


Multiple REPLs may be started against the same running instance of node.  Each
will share the same global object but will have unique I/O.


多个REPLs交互环境可以作为node相同运行中的实例启动。每个REPL共享相同的
全局对象但是它们拥有自己独立的I/O流。


Here is an example that starts a REPL on stdin, a Unix socket, and a TCP socket:

下面是的例子给出分别以stdin，Unix socket及TCPsocket方式启动REPL的示例：


    var net = require("net"),
        repl = require("repl");

    connections = 0;

    repl.start("node via stdin> ");

    net.createServer(function (socket) {
      connections += 1;
      repl.start("node via Unix socket> ", socket);
    }).listen("/tmp/node-repl-sock");

    net.createServer(function (socket) {
      connections += 1;
      repl.start("node via TCP socket> ", socket);
    }).listen(5001);

Running this program from the command line will start a REPL on stdin.  Other
REPL clients may connect through the Unix socket or TCP socket. `telnet` is useful
for connecting to TCP sockets, and `socat` can be used to connect to both Unix and
TCP sockets.

在命令行中运行这段程序将首先启动一个stdin环境的REPL。接下来的几个REPL终端可以通过Unix或者
TCP的socket进行通信。若要连接到TCP socket， `telnet`是个不错的选择；不过`socat`可以用于连接
Unix 与 TCP socket，通吃。

By starting a REPL from a Unix socket-based server instead of stdin, you can
connect to a long-running node process without restarting it.

若是从一个基于Unix socket的服务器中启动REPL，而不是上述的stdin中启动，你可以
将其连接到一个长期运行的node进程中，避免node重启操作。

### REPL Features REPL特性

Inside the REPL, Control+D will exit.  Multi-line expressions can be input.

在REPL中，操作组合键Control+D可以退出node。可以输入多行表达式。

The special variable `_` (underscore) contains the result of the last expression.

特殊变量`_`（下划线）包含了上一表达式的结果。


    > [ "a", "b", "c" ]
    [ 'a', 'b', 'c' ]
    > _.length
    3
    > _ += 1
    4

The REPL provides access to any variables in the global scope. You can expose a variable
to the REPL explicitly by assigning it to the `context` object associated with each
`REPLServer`.  For example:

在REPL可以访问全局作用域中的任何变量。为了在REPL中访问一个变量，你只要将此变量显性地
分配给相应`REPLServer`的`context`对象。示例如下：

    // repl_test.js
    var repl = require("repl"),
        msg = "message";

    repl.start().context.m = msg;

Things in the `context` object appear as local within the REPL:

`context`对象中的变量在REPL看起来就像是本地变量。

    mjr:~$ node repl_test.js
    > m
    'message'

There are a few special REPL commands:

以下是另外一些特殊的REPL命令：

  - `.break` - While inputting a multi-line expression, sometimes you get lost
    or just don't care about completing it. `.break` will start over.

     `.break` - 当你输入多行表达式，有时你会迷糊或者不想完整输入，这时`.break`就可以帮你跳出。
  - `.clear` - Resets the `context` object to an empty object and clears any multi-line expression.

    `.clear` - 该命令翻重置了 `context`对象
  - `.exit` - Close the I/O stream, which will cause the REPL to exit.

    `.exit` - 该命令用于关闭I/O流，并退出REPL。
  - `.help` - Show this list of special commands.

    `.help` - 输出特殊命令的列表。

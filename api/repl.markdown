## REPL 交互式解释器

A Read-Eval-Print-Loop (REPL) is available both as a standalone program and easily
includable in other programs.  REPL provides a way to interactively run
JavaScript and see the results.  It can be used for debugging, testing, or
just trying things out.


交互式解释器（REPL）既可以作为一个独立的程序运行，也可以很容易地包含在其他程序中作为整体程序的一部分使用。REPL为运行JavaScript脚本与查看运行结果提供了一种交互方式，通常REPL交互方式可以用于调试、测试以及试验某种想法。

By executing `node` without any arguments from the command-line you will be
dropped into the REPL. It has simplistic emacs line-editing.

在命令行中不带任何参数地运行`node`命令，就可以进入REPL环境，在该环境下你可以进行一些类似Emacs的行编辑操作。

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

为了进行高级的行编辑操作，可以设置环境变量`NODE_NO_READLINE=1`并启动node。这种情况REPL会进入标准终端设置模式，这此模式下你可以使用`rlwrap`。

For example, you could add this to your bashrc file:

比如，你可以把下列设置添加到你的bashrc文件中：

    alias node="env NODE_NO_READLINE=1 rlwrap node"


### repl.start(prompt='> ', stream=process.stdin)

Starts a REPL with `prompt` as the prompt and `stream` for all I/O.  `prompt`
is optional and defaults to `> `.  `stream` is optional and defaults to
`process.stdin`.


启动一个REPL，使用`prompt`作为输入提示符，在`stream`上进行所有I/O操作。`prompt`是可选参数，默认值为`> `，`stream`也是可选参数，默认值为`process.stdin`。

Multiple REPLs may be started against the same running instance of node.  Each
will share the same global object but will have unique I/O.

一个node实例中可以启动多个REPL，它们共享相同的全局对象，但拥有各自独立的I/O。

Here is an example that starts a REPL on stdin, a Unix socket, and a TCP socket:

下面是的例子展示分别在标准输入，Unix套接字及TCP套接字上启动的REPL示例：


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

在命令行中运行这段程序将首先使用标准输入启动REPL。其他的REPL终端可以通过Unix套接字或者TCP套接字进行连接。可使用`telnet`程序连接到TCP套接字，而`socat`程序既可以连接到Unix套接字也可以连接连接到TCP套接字。

By starting a REPL from a Unix socket-based server instead of stdin, you can
connect to a long-running node process without restarting it.

若要连接到一个长时间运行的node进程而无需重启进程，你应该将REPL启动在Unix套接字上，非不要将其启动在标准输出上。

### REPL Features REPL特性

Inside the REPL, Control+D will exit.  Multi-line expressions can be input.

在REPL中，操作组合键Control+D可以退出。可以输入多行表达式。

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

在REPL可以访问全局作用域中的任何变量。为了在REPL中访问一个变量，你只要将此变量显性地分配给相应`REPLServer`的`context`对象。示例如下：

    // repl_test.js
    var repl = require("repl"),
        msg = "message";

    repl.start().context.m = msg;

Things in the `context` object appear as local within the REPL:

`context`对象中的变量在REPL中看起来就像是本地变量。

    mjr:~$ node repl_test.js
    > m
    'message'

There are a few special REPL commands:

以下是另外一些特殊的REPL命令：

  - `.break` - While inputting a multi-line expression, sometimes you get lost
    or just don't care about completing it. `.break` will start over.
    `.break` - 当你输入多行表达式，如果想放弃当前的输入，可以用`.break`跳出。
  - `.clear` - Resets the `context` object to an empty object and clears any multi-line expression.
    `.clear` - 将`context`重置为空对象，并清空当前正在输入的多行表达式。
  - `.exit` - Close the I/O stream, which will cause the REPL to exit.
    `.exit` - 该命令用于关闭I/O流，并退出REPL。
  - `.help` - Show this list of special commands.
    `.help` - 输出特殊命令的列表。

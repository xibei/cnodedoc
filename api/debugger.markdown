## Debugger 调试器


V8 comes with an extensive debugger which is accessible out-of-process via a
simple [TCP protocol](http://code.google.com/p/v8/wiki/DebuggerProtocol).
Node has a built-in client for this debugger. To use this, start Node with the
`debug` argument; a prompt will appear:

V8引擎自身配备了全面的调试器，该调试器通过简单的[TCP协议](http://code.google.com/p/v8/wiki/DebuggerProtocol)在进程外访问。Node中内置了该调试器的客户端，要使用它可以在启动Node时附加`debug`参数，此模式下将显示debug提示符：

    % node debug myscript.js
    debug>

At this point `myscript.js` is not yet running. To start the script, enter
the command `run`. If everything works okay, the output should look like
this:

此时 `myscript.js`还没有开始执行，若要执行这段脚本，还需要输入`run`命令。如果一切运行正常的话输出信息应该如下所示：

    % node debug myscript.js
    debug> run
    debugger listening on port 5858
    connecting...ok

Node's debugger client doesn't support the full range of commands, but
simple step and inspection is possible. By putting the statement `debugger;`
into the source code of your script, you will enable a breakpoint.

Node的调试器客户端虽然没有支持所有的命令，但是实现简单的单步调试还是可以的。你可以在脚本代码中声明`debugger;`语句从而启用一个断点。

For example, suppose `myscript.js` looked like this:

例如，假设`myscript.js`代码如下：

    // myscript.js
    x = 5;
    setTimeout(function () {
      debugger;
      console.log("world");
    }, 1000);
    console.log("hello");

Then once the debugger is run, it will break on line 4.

一旦调试器开始运行，那么它将在执行到第4行代码处时停止。

    % ./node debug myscript.js
    debug> run
    debugger listening on port 5858
    connecting...ok
    hello
    break in #<an Object>._onTimeout(), myscript.js:4
      debugger;
      ^
    debug> next
    break in #<an Object>._onTimeout(), myscript.js:5
      console.log("world");
      ^
    debug> print x
    5
    debug> print 2+2
    4
    debug> next
    world
    break in #<an Object>._onTimeout() returning undefined, myscript.js:6
    }, 1000);
    ^
    debug> quit
    A debugging session is active. Quit anyway? (y or n) y
    %


The `print` command allows you to evaluate variables. The `next` command steps
over to the next line. There are a few other commands available and more to
come type `help` to see others.

`print`命令允许将变量输出到控制台进行查看。`next`命令单步调试到下一行代码。还有其他一些可用的命令你可以通过输入`help`进行查看。


### Advanced Usage 高级用法

The V8 debugger can be enabled and accessed either by starting Node with
the `--debug` command-line flag or by signaling an existing Node process
with `SIGUSR1`.

要启用V8引擎调试器你可以在启动Node时增加命令行参数`--debug`或者给一个已经存在的Node进程发送值为`SIGUSR1`的信号量。



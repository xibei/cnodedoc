## Child Processes  子进程

Node provides a tri-directional `popen(3)` facility through the `ChildProcess`
class.

在Node里，`ChildProcess`类提供了一个3向的`popen(3)`机制。

It is possible to stream data through the child's `stdin`, `stdout`, and
`stderr` in a fully non-blocking way.

子进程类中的`stdin`， `stdout`，和`stderr` 可以使数据流完全非阻塞式地（non-blocking way）流动（stream）

To create a child process use `require('child_process').spawn()`.

调用`require('child_process').spawn()`可以创建一个子进程（child process） 

Child processes always have three streams associated with them. `child.stdin`,
`child.stdout`, and `child.stderr`.

`child.stdin`，`child.stdout`，和 `child.stderr`等3个流总是伴随着子进程。

`ChildProcess` is an `EventEmitter`.

`ChildProcess` 是一种 `EventEmitter`（事件触发器）。

### Event:  'exit' 事件：'exit'

`function (code, signal) {}`

This event is emitted after the child process ends. If the process terminated
normally, `code` is the final exit code of the process, otherwise `null`. If
the process terminated due to receipt of a signal, `signal` is the string name
of the signal, otherwise `null`.

当子进程结束时，（Node）触发如下事件。如果进程正常终结，那么进程的最终退出代码（ final exit code）为`code`，否则为`null`。如果进程的结束是因为接收到一个信号，那么`signal`为string型的信号名称，否则为`null`。

See `waitpid(2)`.

可见`waitpid(2)`。

### child.stdin

A `Writable Stream` that represents the child process's `stdin`.
Closing this stream via `end()` often causes the child process to terminate.

它是一个`Writable Stream`（可写流），是子进程中的`stdin`。调用`end()`来关闭这个流通常会终结整个进程。

### child.stdout

A `Readable Stream` that represents the child process's `stdout`.

它是一个`Readable Stream`（可读流），是子进程中的`stdout`（标准输出）。

### child.stderr

A `Readable Stream` that represents the child process's `stderr`.

它是一个`Readable Stream`（可读流），是子进程中的`stderr`（标准错误）。

### child.pid

The PID of the child process.

它是子进程的PID（进程编号）。

Example:

例如：

    var spawn = require('child_process').spawn,
        grep  = spawn('grep', ['ssh']);

    console.log('Spawned child pid: ' + grep.pid);
    grep.stdin.end();


### child_process.spawn(command, args=[], [options])

Launches a new process with the given `command`, with  command line arguments in `args`.
If omitted, `args` defaults to an empty Array.

使用指定的`command`创建一个新进程，命令行参数为`args`。缺省下，`args`默认为一个空数组。

The third argument is used to specify additional options, which defaults to:

第三个参数用于指定附加的选项，默认如下：

    { cwd: undefined,
      env: process.env,
      customFds: [-1, -1, -1],
      setsid: false
    }

`cwd` allows you to specify the working directory from which the process is spawned.
Use `env` to specify environment variables that will be visible to the new process.
With `customFds` it is possible to hook up the new process' [stdin, stout, stderr] to
existing streams; `-1` means that a new stream should be created. `setsid`,
if set true, will cause the subprocess to be run in a new session.

参数`cwd` 可以使当前目录从发出进程的路径转向指定的工作目录。参数`env`可以指定哪些环境变量在新进程中是可见的。参数`customFds`可以使新进程中的[stdin, stout, stderr]和已存在的流进行挂接（hook up）。参数`-1`可以建立一个新的流。如果设置参数`setsid`为true，该子进程将转入到一个新会话（session）中运行。

Example of running `ls -lh /usr`, capturing `stdout`, `stderr`, and the exit code:

例：运行`ls -lh /usr`命令，捕获`stdout`，`stderr`和退出代码：

    var util   = require('util'),
        spawn = require('child_process').spawn,
        ls    = spawn('ls', ['-lh', '/usr']);

    ls.stdout.on('data', function (data) {
      console.log('stdout: ' + data);
    });

    ls.stderr.on('data', function (data) {
      console.log('stderr: ' + data);
    });

    ls.on('exit', function (code) {
      console.log('child process exited with code ' + code);
    });


Example: A very elaborate way to run 'ps ax | grep ssh'

例：运行'ps ax | grep ssh'命令的完整方法：

    var util   = require('util'),
        spawn = require('child_process').spawn,
        ps    = spawn('ps', ['ax']),
        grep  = spawn('grep', ['ssh']);

    ps.stdout.on('data', function (data) {
      grep.stdin.write(data);
    });

    ps.stderr.on('data', function (data) {
      console.log('ps stderr: ' + data);
    });

    ps.on('exit', function (code) {
      if (code !== 0) {
        console.log('ps process exited with code ' + code);
      }
      grep.stdin.end();
    });

    grep.stdout.on('data', function (data) {
      console.log(data);
    });

    grep.stderr.on('data', function (data) {
      console.log('grep stderr: ' + data);
    });

    grep.on('exit', function (code) {
      if (code !== 0) {
        console.log('grep process exited with code ' + code);
      }
    });


Example of checking for failed exec:

检测exec执行是否失败的例子：

    var spawn = require('child_process').spawn,
        child = spawn('bad_command');

    child.stderr.on('data', function (data) {
      if (/^execvp\(\)/.test(data.asciiSlice(0,data.length))) {
        console.log('Failed to start child process.');
      }
    });


See also: `child_process.exec()`

可参见：`child_process.exec()`


### child_process.exec(command, [options], callback)

High-level way to execute a command as a child process, buffer the
output, and return it all in a callback.

以子进程方式执行一个命令的高级方法。所有输出经过缓冲后在同一个回调函数中返回。

    var util   = require('util'),
        exec  = require('child_process').exec,
        child;

    child = exec('cat *.js bad_file | wc -l',
      function (error, stdout, stderr) {
        console.log('stdout: ' + stdout);
        console.log('stderr: ' + stderr);
        if (error !== null) {
          console.log('exec error: ' + error);
        }
    });

The callback gets the arguments `(error, stdout, stderr)`. On success, `error`
will be `null`.  On error, `error` will be an instance of `Error` and `err.code`
will be the exit code of the child process, and `err.signal` will be set to the
signal that terminated the process.

回调函数获得`(error, stdout, stderr)`3个参数。成功时，`error`为`null`。错误时`error`为一个`Error`实例，`err.code`为该子进程的退出代码，`err.signal`为使该进程结束的信号。

There is a second optional argument to specify several options. The default options are

可选的第二个参数用于指定一些选项。默认选项如下：

    { encoding: 'utf8',
      timeout: 0,
      maxBuffer: 200*1024,
      killSignal: 'SIGTERM',
      cwd: null,
      env: null }

If `timeout` is greater than 0, then it will kill the child process
if it runs longer than `timeout` milliseconds. The child process is killed with
`killSignal` (default: `'SIGTERM'`). `maxBuffer` specifies the largest
amount of data allowed on stdout or stderr - if this value is exceeded then
the child process is killed.

如果参数`timeout`的值超过0，那么当运行超过`timeout`毫秒后子进程将终止。`killSignal` 为终止子进程的信号(默认为： `'SIGTERM'`)。 参数`maxBuffer`指定了stdout或stderr流最大数据量，一旦超过该值，子进程将会终止。


### child.kill(signal='SIGTERM')

Send a signal to the child process. If no argument is given, the process will
be sent `'SIGTERM'`. See `signal(7)` for a list of available signals.

给子进程发送信号。如果没有指定参数，（Node）将会发送`'SIGTERM'`信号。在`signal(7)` 中可查阅到可用的信号列表。

    var spawn = require('child_process').spawn,
        grep  = spawn('grep', ['ssh']);

    grep.on('exit', function (code, signal) {
      console.log('child process terminated due to receipt of signal '+signal);
    });

    // send SIGHUP to process
    grep.kill('SIGHUP');

Note that while the function is called `kill`, the signal delivered to the child
process may not actually kill it.  `kill` really just sends a signal to a process.

注意：虽然函数名为`kill`（杀死），发送的信号并不会真正杀死子进程。`kill`仅仅是向该进程发送一个信号。

See `kill(2)`

参见`kill(2)`

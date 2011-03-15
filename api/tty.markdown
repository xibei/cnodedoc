## TTY 终端模块

Use `require('tty')` to access this module.

可使用`require('tty')`访问此模块。

Example:

示例：

    var tty = require('tty');
    tty.setRawMode(true);
    process.stdin.resume();
    process.stdin.on('keypress', function(char, key) {
      if (key && key.ctrl && key.name == 'c') {
        console.log('graceful exit');
        process.exit()
      }
    });



### tty.open(path, args=[])

Spawns a new process with the executable pointed to by `path` as the session
leader to a new pseudo terminal.

用`path`路径所指向的可执行文件启动一个新的进程，并将其作为一个新的伪终端的控制进程。

Returns an array `[slaveFD, childProcess]`. `slaveFD` is the file descriptor
of the slave end of the pseudo terminal. `childProcess` is a child process
object.

返回一个数组 `[slaveFD, childProcess]`。`slaveFD` 是这个伪终端的从设备文件描述符，`childProcess`是子进程的对象。


### tty.isatty(fd)

Returns `true` or `false` depending on if the `fd` is associated with a
terminal.

当`fd`所表示的文件描述符与一个终端相关联时返回`true`，否则返回`false`。

### tty.setRawMode(mode)

`mode` should be `true` or `false`. This sets the properties of the current
process's stdin fd to act either as a raw device or default.

`mode`参数可以设为`true`或`false`。此方法设置当前进程的stdin（标准输入）为原始设备方式，或默认方式。


### tty.setWindowSize(fd, row, col)

`ioctl`s the window size settings to the file descriptor.

使用`ioctl`设置文件描述符对应的终端窗口大小（行数与列数）。


### tty.getWindowSize(fd)

Returns `[row, col]` for the TTY associated with the file descriptor.

返回文件描述符所对应的终端的窗口大小`[row, col]`（行数与列数）。


## TTY 终端模块

Use `require('tty')` to access this module.

可通过`require('tty')`调用访问此模块。


### tty.open(path, args=[])

Spawns a new process with the executable pointed to by `path` as the session
leader to a new pseudo terminal.

Returns an array `[slaveFD, childProcess]`. `slaveFD` is the file descriptor
of the slave end of the pseudo terminal. `childProcess` is a child process
object.


### tty.isatty(fd)

Returns `true` or `false` depending on if the `fd` is associated with a
terminal.

当`fd`关联到一个终端时返回`true`，否则返回`false`。

### tty.setRawMode(mode)

`mode` should be `true` or `false`. This sets the properies of the current
process's stdin fd to act either as a raw device or default.


### tty.setWindowSize(fd, row, col)

`ioctl`s the window size settings to the file descriptor.


### tty.getWindowSize(fd)

Returns `[row, col]` for the TTY associated with the file descriptor.



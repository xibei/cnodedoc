## File System

File I/O is provided by simple wrappers around standard POSIX functions.  To
use this module do `require('fs')`. All the methods have asynchronous and
synchronous forms.

文件的I/O是由标准POSIX函数封装而成。需要使用"require('fs')"操作这个模块。所有的方法都提供了异步和同步两种方式。 

The asynchronous form always take a completion callback as its last argument.
The arguments passed to the completion callback depend on the method, but the
first argument is always reserved for an exception. If the operation was
completed successfully, then the first argument will be `null` or `undefined`.

异步形式下，方法的最后一个参数需要传入一个完整的回调函数（callback）。传给回调函数的参数取决于具体的异步方法，但第一个参数总是保留给异常对象。如果操作成功，那么该异常对象就变为null或者undefined。

Here is an example of the asynchronous version:

这里是一个异步调用的例子：

    var fs = require('fs');

    fs.unlink('/tmp/hello', function (err) {
      if (err) throw err;
      console.log('successfully deleted /tmp/hello');
    });

Here is the synchronous version:

这里是一个同步调用的例子：

    var fs = require('fs');

    fs.unlinkSync('/tmp/hello')
    console.log('successfully deleted /tmp/hello');

With the asynchronous methods there is no guaranteed ordering. So the
following is prone to error:

由于异步方法调用无法保证执行的顺序，所以下面的代码容易导致出现错误。

    fs.rename('/tmp/hello', '/tmp/world', function (err) {
      if (err) throw err;
      console.log('renamed complete');
    });
    fs.stat('/tmp/world', function (err, stats) {
      if (err) throw err;
      console.log('stats: ' + JSON.stringify(stats));
    });

It could be that `fs.stat` is executed before `fs.rename`.
The correct way to do this is to chain the callbacks.

这样做有可能导致`fs.stat`在`fs.rename`之前执行，正确的做法是嵌套回传函数。 

    fs.rename('/tmp/hello', '/tmp/world', function (err) {
      if (err) throw err;
      fs.stat('/tmp/world', function (err, stats) {
        if (err) throw err;
        console.log('stats: ' + JSON.stringify(stats));
      });
    });

In busy processes, the programmer is _strongly encouraged_ to use the
asynchronous versions of these calls. The synchronous versions will block
the entire process until they complete--halting all connections.

当需要频繁操作时，建议使用异步方式调用此方法。同步方式在其完成之前将会阻塞当前的整个进程，即搁置所有连接。 

### fs.rename(path1, path2, [callback])

Asynchronous rename(2). No arguments other than a possible exception are given
to the completion callback.

异步调用rename(2)，重命名某个文件，除非回调函数执行过程出现了异常，否则不会传递任何参数。

### fs.renameSync(path1, path2)

Synchronous rename(2).

同步调用重命名rename(2)，重命名某个文件，。

### fs.truncate(fd, len, [callback])

Asynchronous ftruncate(2). No arguments other than a possible exception are
given to the completion callback.

异步调用ftruncate(2)，截断某个文件，除非回调函数执行过程出现了异常，否则不会传递任何参数。

### fs.truncateSync(fd, len)

Synchronous ftruncate(2).

同步调用重命名ftruncate(2)，截断某个文件，。

### fs.chmod(path, mode, [callback])

Asynchronous chmod(2). No arguments other than a possible exception are given
to the completion callback.

异步调用chmod(2)，修改文件权限，除非回调函数执行过程出现了异常，否则不会传递任何参数。

### fs.chmodSync(path, mode)

Synchronous chmod(2).

同步调用chmod(2)，修改文件权限。

### fs.stat(path, [callback])

Asynchronous stat(2). The callback gets two arguments `(err, stats)` where
`stats` is a `fs.Stats` object. It looks like this:

异步调用stat(2)，读取文件信息，回调函数将返回两个参数`(err, stats)`，
其中stats是`fs.Stats`的一个对象，如下所示：

    { dev: 2049,
      ino: 305352,
      mode: 16877,
      nlink: 12,
      uid: 1000,
      gid: 1000,
      rdev: 0,
      size: 4096,
      blksize: 4096,
      blocks: 8,
      atime: '2009-06-29T11:11:55Z',
      mtime: '2009-06-29T11:11:40Z',
      ctime: '2009-06-29T11:11:40Z' }

See the `fs.Stats` section below for more information.

有关详细信息，请参阅下面的`fs.Stats`部分

### fs.lstat(path, [callback])

Asynchronous lstat(2). The callback gets two arguments `(err, stats)` where
`stats` is a `fs.Stats` object. lstat() is identical to stat(), except that if
path is a symbolic link, then the link itself is stat-ed, not the file that it
refers to.

异步形式调用lstat(2)，回调函数返回两个参数`(err, stats)`，其中stats是`fs.Stats`的一个对象，lstat()和stat()类似，区别在于当path是一个符号链接时，它指向该连接的属性，而不是所指向文件的属性

### fs.fstat(fd, [callback])

Asynchronous fstat(2). The callback gets two arguments `(err, stats)` where
`stats` is a `fs.Stats` object.

异步形式调用fstat(2)，回调函数返回两个参数`(err, stats)`，其中stats是`fs.Stats`的一个对象

### fs.statSync(path)

Synchronous stat(2). Returns an instance of `fs.Stats`.

同步形式调用stat(2)，返回`fs.Stats`的一个实例。

### fs.lstatSync(path)

Synchronous lstat(2). Returns an instance of `fs.Stats`.

同步形式调用lstat(2)，返回`fs.Stats`的一个实例。

### fs.fstatSync(fd)

Synchronous fstat(2). Returns an instance of `fs.Stats`.

同步形式调用fstatSync(2)，返回`fs.Stats`的一个实例。

### fs.link(srcpath, dstpath, [callback])

Asynchronous link(2). No arguments other than a possible exception are given to
the completion callback.

异步调用link(2)，创建符号连接，除非回调函数执行过程出现了异常，否则不会传递任何参数。

### fs.linkSync(srcpath, dstpath)

Synchronous link(2).

同步调用link(2)。

### fs.symlink(linkdata, path, [callback])

Asynchronous symlink(2). No arguments other than a possible exception are given
to the completion callback.

异步调用symlink(2)，除非回调函数执行过程出现了异常，否则不会传递任何参数。

### fs.symlinkSync(linkdata, path)

Synchronous symlink(2).

同步调用symlink(2)。

### fs.readlink(path, [callback])

Asynchronous readlink(2). The callback gets two arguments `(err,
resolvedPath)`.

异步调用readlink，回调函数返回两个参数`(err,resolvedPath)`，resolvedPath为解析后的文件路径。

### fs.readlinkSync(path)

Synchronous readlink(2). Returns the resolved path.

同步调用readlink(2)，返回解析后的文件路径。

### fs.realpath(path, [callback])

Asynchronous realpath(2).  The callback gets two arguments `(err,
resolvedPath)`.

异步调用realpath，回调函数返回两个参数`(err,resolvedPath)`，resolvedPath为解析后的文件路径。

### fs.realpathSync(path)

Synchronous realpath(2). Returns the resolved path.

### fs.unlink(path, [callback])

Asynchronous unlink(2). No arguments other than a possible exception are given
to the completion callback.

异步调用unlink(2)，删除连接或者文件，除非回调函数执行过程出现了异常，否则不会传递任何参数。

### fs.unlinkSync(path)

Synchronous unlink(2).

同步调用unlink(2)。

### fs.rmdir(path, [callback])

Asynchronous rmdir(2). No arguments other than a possible exception are given
to the completion callback.

异步调用rmdir(2)，除非回调函数执行过程出现了异常，否则不会传递任何参数。

### fs.rmdirSync(path)

Synchronous rmdir(2).

同步调用rmdir(2)。

### fs.mkdir(path, mode, [callback])

Asynchronous mkdir(2). No arguments other than a possible exception are given
to the completion callback.

异步调用mkdir(2)，除非回调函数执行过程出现了异常，否则不会传递任何参数。

### fs.mkdirSync(path, mode)

Synchronous mkdir(2).

同步调用mkdir(2)。

### fs.readdir(path, [callback])

Asynchronous readdir(3).  Reads the contents of a directory.
The callback gets two arguments `(err, files)` where `files` is an array of
the names of the files in the directory excluding `'.'` and `'..'`.

异步读取目录中的内容(readdir(3))。回调函数的第二个参数是以阵列构成的目录内对象的名称('.'与'..'除外)。(err, files) 

### fs.readdirSync(path)

Synchronous readdir(3). Returns an array of filenames excluding `'.'` and
`'..'`.

同步读取目录中的内容(readdir(3))。返回以阵列构成的目录内对象名称('.'与'..'除外)。 

### fs.close(fd, [callback])

Asynchronous close(2).  No arguments other than a possible exception are given
to the completion callback.

异步同步调用close(2)，关闭文件，除非回调函数执行过程出现了异常，否则不会传递任何参数。

### fs.closeSync(fd)

Synchronous close(2).

同步调用close(2)。

### fs.open(path, flags, mode=0666, [callback])

Asynchronous file open. See open(2). Flags can be 'r', 'r+', 'w', 'w+', 'a',
or 'a+'. The callback gets two arguments `(err, fd)`.

异步开启文件，详阅 open(2)。标签可为'r', 'r+', 'w', 'w+', 'a', 或 'a+'。回调函数的第二个参数是指标。(err, fd) 

### fs.openSync(path, flags, mode=0666)

Synchronous open(2).

同步调用open(2)。

### fs.utimes(path, atime, mtime, callback)
### fs.utimesSync(path, atime, mtime)

Change file timestamps.

更改文件时间戳。

### fs.futimes(path, atime, mtime, callback)
### fs.futimesSync(path, atime, mtime)

Change file timestamps with the difference that if filename refers to a
symbolic link, then the link is not dereferenced.

另一种更改文件时间戳的方式。区别在于如果文件名指向一个符号连接，则改变此符号连接的时间戳，而不改变所引用文件的时间戳。

### fs.write(fd, buffer, offset, length, position, [callback])

Write `buffer` to the file specified by `fd`.

根据`fd`参数将`buffer`内容按照指定方式写入文件。

`offset` and `length` determine the part of the buffer to be written.

`offset` 和 `length` 决定了将buffer中的那部分写入文件。

`position` refers to the offset from the beginning of the file where this data
should be written. If `position` is `null`, the data will be written at the
current position.
See pwrite(2).

position指向待写入数据按文件从头部算起的偏移位置，若position为空，数据将从当前位置开始写入，详阅pwrite(2)

The callback will be given two arguments `(err, written)` where `written`
specifies how many _bytes_ were written.

回调函数返回两个参数`(err, written)`,其中`written`标识有多少字节的数据已经写入。

### fs.writeSync(fd, buffer, offset, length, position)

Synchronous version of buffer-based `fs.write()`. Returns the number of bytes
written.

基于buffer的同步调用`fs.write()`的版本，返回写入数据的字节数。

### fs.writeSync(fd, str, position, encoding='utf8')

Synchronous version of string-based `fs.write()`. Returns the number of bytes
written.

基于string的同步调用`fs.write()`的版本，返回写入数据的字节数。

### fs.read(fd, buffer, offset, length, position, [callback])

Read data from the file specified by `fd`.

按照`fd`指定的方式读取文件。

`buffer` is the buffer that the data will be written to.

`buffer`为写入数据的缓冲区

`offset` is offset within the buffer where writing will start.

`offset`为开始写入到缓冲区的偏移地址

`length` is an integer specifying the number of bytes to read.

`length`指明了待读取数据的字节数。

`position` is an integer specifying where to begin reading from in the file.
If `position` is `null`, data will be read from the current file position.

`position`为一个整形变量，标识从哪个位置开始读取文件，如果`position`参数为`null`，数据将从文件当前位置开始读取。

The callback is given the two arguments, `(err, bytesRead)`.

回调函数返回两个参数，`(err, bytesRead)`。

### fs.readSync(fd, buffer, offset, length, position)

Synchronous version of buffer-based `fs.read`. Returns the number of
`bytesRead`.

基于buffer的同步调用`fs.read`的版本，返回已经读入的数据的字节数量。

### fs.readSync(fd, length, position, encoding)

Synchronous version of string-based `fs.read`. Returns the number of
`bytesRead`.

基于string的同步调用`fs.read`的版本，返回已经读入的数据的字节数量。

### fs.readFile(filename, [encoding], [callback])

Asynchronously reads the entire contents of a file. Example:

异步读取一个文件的所有内容，例子如下：

    fs.readFile('/etc/passwd', function (err, data) {
      if (err) throw err;
      console.log(data);
    });

The callback is passed two arguments `(err, data)`, where `data` is the
contents of the file.

回调函数将传入两个参数`(err, data)`，其中data为文件内容。

If no encoding is specified, then the raw buffer is returned.

如果没有设置编码，那么将返回原始的缓冲区内容。

### fs.readFileSync(filename, [encoding])

Synchronous version of `fs.readFile`. Returns the contents of the `filename`.

同步调用`fs.readFile`的版本，返回指定文件名`filename`的内容。

If `encoding` is specified then this function returns a string. Otherwise it
returns a buffer.

如果函数中没有设置编码`encoding`，那么将返回原始的缓冲区内容。

### fs.writeFile(filename, data, encoding='utf8', [callback])

Asynchronously writes data to a file. `data` can be a string or a buffer.

异步写入数据到某个文件中，`data`可以是字符串或者缓冲区。

Example:

例子：

    fs.writeFile('message.txt', 'Hello Node', function (err) {
      if (err) throw err;
      console.log('It\'s saved!');
    });

### fs.writeFileSync(filename, data, encoding='utf8')

The synchronous version of `fs.writeFile`.

同步调用`fs.writeFile`的方式。

### fs.watchFile(filename, [options], listener)

Watch for changes on `filename`. The callback `listener` will be called each
time the file is accessed.

观察指定文件名`filename`的文件的变化，回调函数`listener`将在每次该文件被访问时被调用。

The second argument is optional. The `options` if provided should be an object
containing two members a boolean, `persistent`, and `interval`, a polling
value in milliseconds. The default is `{ persistent: true, interval: 0 }`.

第二个参数是可选项，`options`参数应当以一个对象的形式提供，且该对象包含两个布尔类型的成员属性，属性名为`persistent`，和 `interval` 轮询的时间间隔，默认值为`{ persistent: true, interval: 0 }`。

The `listener` gets two arguments the current stat object and the previous
stat object:

该监听器将获得两个参数，分别标识当前的统计对象和前一次的统计对象。

    fs.watchFile(f, function (curr, prev) {
      console.log('the current mtime is: ' + curr.mtime);
      console.log('the previous mtime was: ' + prev.mtime);
    });

These stat objects are instances of `fs.Stat`.

这些统计对象为`fs.Stat`的实例。

If you want to be notified when the file was modified, not just accessed
you need to compare `curr.mtime` and `prev.mtime.

如果你想在文件被修改而不是被访问时得到通知，你还需要比较`curr.mtime` 和 `prev.mtime`的值。

### fs.unwatchFile(filename)

Stop watching for changes on `filename`.

停止观察文件`filename`的变化。

## fs.Stats

Objects returned from `fs.stat()` and `fs.lstat()` are of this type.

`fs.stat()` 和 `fs.lstat()`方法返回的对象为这种类型

 - `stats.isFile()`
 - `stats.isDirectory()`
 - `stats.isBlockDevice()`
 - `stats.isCharacterDevice()`
 - `stats.isSymbolicLink()` (only valid with  `fs.lstat()`)
 - `stats.isFIFO()`
 - `stats.isSocket()`


## fs.ReadStream

`ReadStream` is a `Readable Stream`.

`ReadStream`是可读取的流。

### fs.createReadStream(path, [options])

Returns a new ReadStream object (See `Readable Stream`).

返回一个新的ReadStream对象（参见`Readable Stream`）。

`options` is an object with the following defaults:

可选项为一个对象，它包含如下默认值：

    { flags: 'r',
      encoding: null,
      fd: null,
      mode: 0666,
      bufferSize: 64 * 1024
    }

`options` can include `start` and `end` values to read a range of bytes from
the file instead of the entire file.  Both `start` and `end` are inclusive and
start at 0.  When used, both the limits must be specified always.

选项中还包括了`start` 和 `end`参数值表示需要读取文件从`start` 到 `end`这个范围字节内容而不是读取整个文件，`start` 和 `end`包含在选项中并且start默认为0，一旦启用，这两个参数都需要设置。

An example to read the last 10 bytes of a file which is 100 bytes long:

一个例子演示了从一个长度为100字节的文件中读取最后10个字节流

    fs.createReadStream('sample.txt', {start: 90, end: 99});


## fs.WriteStream

`WriteStream` is a `Writable Stream`.

`WriteStream` 为可写入的数据流。

### Event: 'open'

`function (fd) { }`

 `fd` is the file descriptor used by the WriteStream.
 
 `fd`指定了使用WriteStream的文件描述符。

### fs.createWriteStream(path, [options])

Returns a new WriteStream object (See `Writable Stream`).

返回一个新的WriteStream对象（参见`Writable Stream`）。

`options` is an object with the following defaults:

可选项为一个对象，它包含如下默认值：

    { flags: 'w',
      encoding: null,
      mode: 0666 }

## Path 路径模块

This module contains utilities for dealing with file paths.  Use
`require('path')` to use it.  It provides the following methods:

该模块包括了一些处理文件路径的功能，可以通过`require('path')`方法来使用它。该模块提供了如下的方法：

### path.normalize(p)

Normalize a string path, taking care of `'..'` and `'.'` parts.

该方法用于标准化一个字符型的路径，请注意`'..'` 与 `'.'` 的使用。

When multiple slashes are found, they're replaces by a single one;
when the path contains a trailing slash, it is preserved.
On windows backslashes are used. 

当发现有多个斜杠（/）时，系统会将他们替换为一个斜杠；如果路径末尾中包含有一个斜杠，那么系统会保留这个斜杠。在Windows中，上述路径中的斜杠（/）要换成反斜杠（\）。

Example:

示例：

    path.normalize('/foo/bar//baz/asdf/quux/..')
    // returns
    '/foo/bar/baz/asdf'

### path.join([path1], [path2], [...])

Join all arguments together and normalize the resulting path.

该方法用于合并方法中的各参数并得到一个标准化合并的路径字符串。

Example:

示例：

    node> require('path').join(
    ...   '/foo', 'bar', 'baz/asdf', 'quux', '..')
    '/foo/bar/baz/asdf'

### path.resolve([from ...], to)

Resolves `to` to an absolute path.

将`to`参数解析为绝对路径。

If `to` isn't already absolute `from` arguments are prepended in right to left
order, until an absolute path is found. If after using all `from` paths still
no absolute path is found, the current working directory is used as well. The
resulting path is normalized, and trailing slashes are removed unless the path 
gets resolved to the root directory.

如果参数 `to`当前不是绝对的，系统会将`from` 参数按从右到左的顺序依次前缀到`to`上，直到在`from`中找到一个绝对路径时停止。如果遍历所有`from`中的路径后，系统依然没有找到一个绝对路径，那么当前工作目录也会作为参数使用。最终得到的路径是标准化的字符串，并且标准化时系统会自动删除路径末尾的斜杠，但是如果获取的路径是解析到根目录的，那么系统将保留路径末尾的斜杠。

Another way to think of it is as a sequence of `cd` commands in a shell.

你也可以将这个方法理解为Shell中的一组`cd`命令。

    path.resolve('foo/bar', '/tmp/file/', '..', 'a/../subfile')

Is similar to:

就类似于：

    cd foo/bar
    cd /tmp/file/
    cd ..
    cd a/../subfile
    pwd

The difference is that the different paths don't need to exist and may also be
files.

该方法与`cd`命令的区别在于该方法中不同的路径不一定存在，而且这些路径也可能是文件。

Examples:

示例：

    path.resolve('/foo/bar', './baz')
    // returns
    '/foo/bar/baz'

    path.resolve('/foo/bar', '/tmp/file/')
    // returns
    '/tmp/file'

    path.resolve('wwwroot', 'static_files/png/', '../gif/image.gif')
    // if currently in /home/myself/node, it returns
    '/home/myself/node/wwwroot/static_files/gif/image.gif'

### path.dirname(p)

Return the directory name of a path.  Similar to the Unix `dirname` command.

该方法返回一个路径的目录名，类似于Unix中的`dirname`命令。

Example:

示例：

    path.dirname('/foo/bar/baz/asdf/quux')
    // returns
    '/foo/bar/baz/asdf'

### path.basename(p, [ext])

Return the last portion of a path.  Similar to the Unix `basename` command.

该方法返回一个路径中最低一级目录名，类似于Unix中的 `basename`命令。

Example:

示例：

    path.basename('/foo/bar/baz/asdf/quux.html')
    // returns
    'quux.html'

    path.basename('/foo/bar/baz/asdf/quux.html', '.html')
    // returns
    'quux'

### path.extname(p)

Return the extension of the path.  Everything after the last '.' in the last portion
of the path.  If there is no '.' in the last portion of the path or the only '.' is
the first character, then it returns an empty string.  

该方法返回路径中的文件扩展名，即路径最低一级的目录中'.'字符后的任何字符串。如果路径最低一级的目录中'没有'.' 或者只有'.'，那么该方法返回一个空字符串。

Examples:

示例：

    path.extname('index.html')
    // returns
    '.html'

    path.extname('index')
    // returns
    ''

### path.exists(p, [callback])

Test whether or not the given path exists.  Then, call the `callback` argument
with either true or false. Example:

该方法用于测试参数`p`中的路径是否存在。然后以true 或者 false的方式调用`callback`参数。示例：

    path.exists('/etc/passwd', function (exists) {
      util.debug(exists ? "it's there" : "no passwd!");
    });


### path.existsSync(p)

Synchronous version of `path.exists`.

`path.exists`的同步版本。
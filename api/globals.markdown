## Global Objects

These object are available in the global scope and can be accessed from anywhere.

这些对象在全局范围内均可用，你可以在任何位置访问这些对象。

### global

The global namespace object.

全局命名空间对象

In browsers, the top-level scope is the global scope. That means that in
browsers if you're in the global scope `var something` will define a global
variable. In Node this is different. The top-level scope is not the global
scope; `var something` inside a Node module will be local to that module.

在浏览器中，顶级作用域为全局作用域，在全局作用域下通过`var something`即定义了一个全局变量。但是在Node中并不如此，顶级作用域并非是全局作用域，在Node模块中通过`var something`定义的变量仅作用于该模块。

### process

The process object. See the 'process object' section.

进程对象，参见'process object'章节

### require()

To require modules. See the 'Modules' section.

加载模块，参见'Modules'章节。

### require.resolve()

Use the internal `require()` machinery to look up the location of a module,
but rather than loading the module, just return the resolved filename.

使用内部函数`require()`遍历查找一个模块的位置，而不用加载模块，只是返回对应的文件名。

### require.paths

An array of search paths for `require()`.  This array can be modified to add
custom paths.

以数组形式返回`require()`的搜索路径，你可以更改该数组添加自定义的搜索路径。


Example: add a new path to the beginning of the search list

例如：将一个新的搜索路径添加到搜索列表中

    require.paths.unshift('/usr/local/node');


### __filename

The filename of the script being executed.  This is the absolute path, and not necessarily
the same filename passed in as a command line argument.

当前正在执行的脚本的文件名，由于它是绝对路径，所以当把它作为命令行参数不会出现文件名重名的情况

Example: running `node example.js` from `/Users/mjr`

例如：在目录`/Users/mjr`下运行`node example.js`

    console.log(__filename);
    // /Users/mjr/example.js

### __dirname

The dirname of the script being executed.

当前正在执行脚本所在的目录名

Example: running `node example.js` from `/Users/mjr`

例如：在目录`/Users/mjr`下运行`node example.js`

    console.log(__dirname);
    // /Users/mjr


### module

A reference to the current module. In particular
`module.exports` is the same as the `exports` object. See `src/node.js`
for more information.

module指向当前模块，特别的，当你通过`module.exports`和`exports`两种方式访问的将是同一个对象，参见`src/node.js`可以获得更多信息

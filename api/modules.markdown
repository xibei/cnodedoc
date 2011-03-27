## Modules 模块


Node uses the CommonJS module system.
Node has a simple module loading system.  In Node, files and modules are in
one-to-one correspondence.  As an example, `foo.js` loads the module
`circle.js` in the same directory.

Node使用CommonJS模块系统。Node有一个简单的模块装载系统，在Node中，文件和模块是一一对应的。下面的例子展示了`foo.js`文件如何在相同的目录中加载`circle.js`模块。

The contents of `foo.js`:

`foo.js`的内容为：

    var circle = require('./circle.js');
    console.log( 'The area of a circle of radius 4 is '
               + circle.area(4));

The contents of `circle.js`:

`circle.js`的内容为：

    var PI = Math.PI;

    exports.area = function (r) {
      return PI * r * r;
    };

    exports.circumference = function (r) {
      return 2 * PI * r;
    };

The module `circle.js` has exported the functions `area()` and
`circumference()`.  To export an object, add to the special `exports`
object.

`circle.js`模块输出了`area()`和`circumference()`两个函数，为了以对象的形式输出，需将要输出的函数加入到一个特殊的`exports`对像中。

Variables
local to the module will be private. In this example the variable `PI` is
private to `circle.js`.

模块的本地变量是私有的。在上面的例子中，变量`PI`就是`circle.js`私有的。

### Core Modules 核心模块

Node has several modules compiled into the binary.  These modules are
described in greater detail elsewhere in this documentation.

Node有一些编译成二进制的模块。这些模块在这篇文档的其他地方有详细描述。

The core modules are defined in node's source in the `lib/` folder.

核心模块在node源代码中的lib文件夹下。

Core modules are always preferentially loaded if their identifier is
passed to `require()`.  For instance, `require('http')` will always
return the built in HTTP module, even if there is a file by that name.

核心模块总是被优先加载，如果它们的标识符被`require()`调用。例如，`require('http')`将总是返回内建的HTTP模块，即便又一个同名文件存在。

### File Modules 文件模块

If the exact filename is not found, then node will attempt to load the
required filename with the added extension of `.js`, and then `.node`.
`.js` files are interpreted as JavaScript text files, and `.node` files
are interpreted as compiled addon modules loaded with `dlopen`.

如果没有找到确切的文件名，node将尝试以追加扩展名`.js`后的文件名读取文件，如果还是没有找到则尝试追加扩展名`.node`。`.js`文件被解释为JavaScript格式的纯文本文件，`.node`文件被解释为编译后的addon（插件）模块，并使用`dlopen`来加载。

A module prefixed with `'/'` is an absolute path to the file.  For
example, `require('/home/marco/foo.js')` will load the file at
`/home/marco/foo.js`.

以`'/'`为前缀的模块是一个指向文件的绝对路径，例如`require('/home/marco/foo.js')`将加载文件`/home/marco/foo.js`。

A module prefixed with `'./'` is relative to the file calling `require()`.
That is, `circle.js` must be in the same directory as `foo.js` for
`require('./circle')` to find it.

以`'./'`为前缀的模块是指向文件的相对路径，相对于调用`require()`的文件。也就是说为了使`require('./circle')` 能找到正确的文件，`circle.js`必须位于与`foo.js` 相同的路径之下。

Without a leading '/' or './' to indicate a file, the module is either a
"core module" or is loaded from a `node_modules` folder.

如果标明一个文件时没有 '/' 或 './'前缀，该模块或是"核心模块"，或者位于 `node_modules`目录中。

### Loading from `node_modules` Folders 从 `node_modules` 目录中加载

If the module identifier passed to `require()` is not a native module,
and does not begin with `'/'`, `'../'`, or `'./'`, then node starts at the
parent directory of the current module, and adds `/node_modules`, and
attempts to load the module from that location.

如果传递到 `require()`的模块标识符不是一个核心模块，并且不是以`'/'`，`'../'`或`'./'`开头，node将从当前模块的父目录开始，在其`/node_modules`子目录中加载该模块。

If it is not found there, then it moves to the parent directory, and so
on, until either the module is found, or the root of the tree is
reached.

如果在那里没有找到，就转移到上一级目录，依此类推，直到找到该模块或到达目录树的根结点。

For example, if the file at `'/home/ry/projects/foo.js'` called
`require('bar.js')`, then node would look in the following locations, in
this order:

例如，如果在文件 `'/home/ry/projects/foo.js'`中调用 `require('bar.js')，node将会依次查找以下位置：

* `/home/ry/projects/node_modules/bar.js`
* `/home/ry/node_modules/bar.js`
* `/home/node_modules/bar.js`
* `/node_modules/bar.js`

This allows programs to localize their dependencies, so that they do not
clash.

这允许程序本地化他们的依赖关系，避免发生冲突。

#### Optimizations to the `node_modules` Lookup Process 优化 `node_modules` 的查找过程

When there are many levels of nested dependencies, it is possible for
these file trees to get fairly long.  The following optimizations are thus
made to the process.

如果有很多级的嵌套信赖，文件树会变得相当的长，下面是对这一过程的一些优化。

First, `/node_modules` is never appended to a folder already ending in
`/node_modules`.

首先， `/node_modules`不要添加到以 `/node_modules`结尾的目录上。

Second, if the file calling `require()` is already inside a `node_modules`
hierarchy, then the top-most `node_modules` folder is treated as the
root of the search tree.

其次，如果调用`require()`的文件已经位于一个`node_modules`层次中，最上级的`node_modules`目录将被作为搜索的根。

For example, if the file at
`'/home/ry/projects/foo/node_modules/bar/node_modules/baz/quux.js'`
called `require('asdf.js')`, then node would search the following
locations:

例如，如果文件`'/home/ry/projects/foo/node_modules/bar/node_modules/baz/quux.js'`调用`require('asdf.js')`，node会在下面的位置进行搜索：

* `/home/ry/projects/foo/node_modules/bar/node_modules/baz/node_modules/asdf.js`
* `/home/ry/projects/foo/node_modules/bar/node_modules/asdf.js`
* `/home/ry/projects/foo/node_modules/asdf.js`

### Folders as Modules 目录作为模块

It is convenient to organize programs and libraries into self-contained
directories, and then provide a single entry point to that library.
There are three ways in which a folder may be passed to `require()` as
an argument.

很方便将程序或库组织成自包含的目录，并提供一个单独的入口指向那个库。有三种方式可以将一个子目录作为参数传递给 `require()` 。

The first is to create a `package.json` file in the root of the folder,
which specifies a `main` module.  An example package.json file might
look like this:

第一种方法是在目录的根下创建一个名为`package.json`的文件，它指定了一个`main` 模块。一个package.jso文件的例子如下面所示：

    { "name" : "some-library",
      "main" : "./lib/some-library.js" }

If this was in a folder at `./some-library`, then
`require('./some-library')` would attempt to load
`./some-library/lib/some-library.js`.

如果此文件位于`./some-library`目录中，`require('./some-library')`将试图加载文件`./some-library/lib/some-library.js`。

This is the extent of Node's awareness of package.json files.

这是Node感知package.json文件的范围。

If there is no package.json file present in the directory, then node
will attempt to load an `index.js` or `index.node` file out of that
directory.  For example, if there was no package.json file in the above
example, then `require('./some-library')` would attempt to load:

如果在目录中没有package.json文件，node将试图在该目录中加载`index.js` 或 `index.node`文件。例如，在上面的例子中没有 package.json文件，`require('./some-library')`将试图加载：

* `./some-library/index.js`
* `./some-library/index.node`

### Caching 缓存

Modules are cached after the first time they are loaded.  This means
(among other things) that every call to `require('foo')` will get
exactly the same object returned, if it would resolve to the same file.

模块在第一次加载后将被缓存。这意味着（类似其他缓存）每次调用`require('foo')`如果解析到相同的文件，那么将返回同一个对象。

### All Together... 总结一下...

To get the exact filename that will be loaded when `require()` is called, use
the `require.resolve()` function.

可使用`require.resolve()`函数，获得调用`require()`时将加载的准确的文件名。

Putting together all of the above, here is the high-level algorithm
in pseudocode of what require.resolve does:

综上所述，这里以伪代码的形式给出require.resolve的算法逻辑：

    require(X)
    1. If X is a core module,
       a. return the core module
       b. STOP
    2. If X begins with `./` or `/`,
       a. LOAD_AS_FILE(Y + X)
       b. LOAD_AS_DIRECTORY(Y + X)
    3. LOAD_NODE_MODULES(X, dirname(Y))
    4. THROW "not found"

    LOAD_AS_FILE(X)
    1. If X is a file, load X as JavaScript text.  STOP
    2. If X.js is a file, load X.js as JavaScript text.  STOP
    3. If X.node is a file, load X.node as binary addon.  STOP

    LOAD_AS_DIRECTORY(X)
    1. If X/package.json is a file,
       a. Parse X/package.json, and look for "main" field.
       b. let M = X + (json main field)
       c. LOAD_AS_FILE(M)
    2. LOAD_AS_FILE(X/index)

    LOAD_NODE_MODULES(X, START)
    1. let DIRS=NODE_MODULES_PATHS(START)
    2. for each DIR in DIRS:
       a. LOAD_AS_FILE(DIR/X)
       b. LOAD_AS_DIRECTORY(DIR/X)

    NODE_MODULES_PATHS(START)
    1. let PARTS = path split(START)
    2. let ROOT = index of first instance of "node_modules" in PARTS, or 0
    3. let I = count of PARTS - 1
    4. let DIRS = []
    5. while I > ROOT,
       a. if PARTS[I] = "node_modules" CONTINUE
       c. DIR = path join(PARTS[0 .. I] + "node_modules")
       b. DIRS = DIRS + DIR
    6. return DIRS

### Loading from the `require.paths` Folders 从`require.paths`目录中加载

In node, `require.paths` is an array of strings that represent paths to
be searched for modules when they are not prefixed with `'/'`, `'./'`, or
`'../'`.  For example, if require.paths were set to:

在node中，`require.paths`是一个保存模块搜索路径的字符串数组。当模块不以`'/'`，`'./'`或`'../'`为前缀时，将从此数组中的路径里进行搜索。例如，如果require.paths如下设置：

    [ '/home/micheil/.node_modules',
      '/usr/local/lib/node_modules' ]

Then calling `require('bar/baz.js')` would search the following
locations:

当调用`require('bar/baz.js')`时将搜索下列位置：

* 1: `'/home/micheil/.node_modules/bar/baz.js'`
* 2: `'/usr/local/lib/node_modules/bar/baz.js'`

The `require.paths` array can be mutated at run time to alter this
behavior.

可以在运行时改变`require.paths`数组的内容，以改变路径搜索行为。

It is set initially from the `NODE_PATH` environment variable, which is
a colon-delimited list of absolute paths.  In the previous example,
the `NODE_PATH` environment variable might have been set to:

此数组使用`NODE_PATH`环境变量进行初始化，此环境变量是冒号分割的路径列表。在之前的例子中，`NODE_PATH`环境变量被设置为如下内容：

    /home/micheil/.node_modules:/usr/local/lib/node_modules

Loading from the `require.paths` locations is only performed if the
module could not be found using the `node_modules` algorithm above.
Global modules are lower priority than bundled dependencies.

只有当使用上面介绍的`node_modules`算法无法找到模块时，才会从`require.paths`地址里进行加载。全局模块比绑定抵赖的模块优先级低。

#### **Note:** Please Avoid Modifying `require.paths` **注意：** 请不要修改`requires.paths`

For compatibility reasons, `require.paths` is still given first priority
in the module lookup process.  However, it may disappear in a future
release.

由于兼容性的原因，`require.paths`仍然在模块查询过程中处于第一优先级。然而，在未来发布的版本中这个问题将被解决。

While it seemed like a good idea at the time, and enabled a lot of
useful experimentation, in practice a mutable `require.paths` list is
often a troublesome source of confusion and headaches.

虽然在当时看起来这是个好注意，可以支持很多有用的实验手段。但在实践中发现，修改`require.paths`列表往往是造成混乱和麻烦的源头。

##### Setting `require.paths` to some other value does nothing. 将`require.paths`设为其他值不会产生任何作用

This does not do what one might expect:

下述做法不会其他你期望的任何效果：

    require.paths = [ '/usr/lib/node' ];

All that does is lose the reference to the *actual* node module lookup
paths, and create a new reference to some other thing that isn't used
for anything.

这么做将会丢失对*真正的*模块搜索路径列表对象的引用，同时指向了一个新创建的对象，而这个对象将不会其任何作用。

##### Putting relative paths in `require.paths` is... weird.  不建议在`require.paths`中发入相对路径

If you do this:

如果你这样做：

    require.paths.push('./lib');

then it does *not* add the full resolved path to where `./lib`
is on the filesystem.  Instead, it literally adds `'./lib'`,
meaning that if you do `require('y.js')` in `/a/b/x.js`, then it'll look
in `/a/b/lib/y.js`.  If you then did `require('y.js')` in
`/l/m/n/o/p.js`, then it'd look in `/l/m/n/o/lib/y.js`.

这样只会添加`'./lib'`字符串到搜索路径列表，而*不会*解析`./lib`在文件系统中的绝对路径。这意味着如果你在`/a/b/x.js`中调用`require('y.js')`，将找到`/a/b/lib/y.js`。而如果你在`/l/m/n/o/p.js`中调用`require('y.js')`，将找到`/l/m/n/o/lib/y.js`。

In practice, people have used this as an ad hoc way to bundle
dependencies, but this technique is brittle.

在实践中，有用户使用这种特别的方式来实现绑定依赖，但这种方式是很脆弱的。

##### Zero Isolation 零隔离

There is (by regrettable design), only one `require.paths` array used by
all modules.

由于设计的失误，所有模块都共享同一个`require.paths`数组。

As a result, if one node program comes to rely on this behavior, it may
permanently and subtly alter the behavior of all other node programs in
the same process.  As the application stack grows, we tend to assemble
functionality, and it is a problem with those parts interact in ways
that are difficult to predict.

造成的结果是，如果一个node程序依赖于这种行为，它将永久的并且隐蔽的改变处在同个进程内的所有其他node程序的行为。一旦应用程序变大，我们往往进行功能集成，各部分功能以不可预料的方式互相影响将成为问题。

## Addenda: Package Manager Tips 附录：包管理技巧

The semantics of Node's `require()` function were designed to be general
enough to support a number of sane directory structures. Package manager
programs such as `dpkg`, `rpm`, and `npm` will hopefully find it possible to
build native packages from Node modules without modification.

Node的`require()`函数的语义被设计的足够通用化，以支持各种常规目录结构。包管理程序如 `dpkg`，`rpm`和`npm`将不用修改就能够从Node模块构建本地包。

Below we give a suggested directory structure that could work:

接下来我们将给你一个可行的目录结构建议：

Let's say that we wanted to have the folder at
`/usr/lib/node/<some-package>/<some-version>` hold the contents of a
specific version of a package.

假设我们希望将一个包的指定版本放在`/usr/lib/node/<some-package>/<some-version>`目录中。

Packages can depend on one another. In order to install package `foo`, you
may have to install a specific version of package `bar`.  The `bar` package
may itself have dependencies, and in some cases, these dependencies may even
collide or form cycles.

包可以依赖于其他包。为了安装包`foo`，可能需要安装包`bar`的一个指定版本。包`bar`也可能有依赖关系，在一些情况下依赖关系可能发生冲突或形成循环。

Since Node looks up the `realpath` of any modules it loads (that is,
resolves symlinks), and then looks for their dependencies in the
`node_modules` folders as described above, this situation is very simple to
resolve with the following architecture:

因为Node会查找它所加载的模块的`真实路径`（也就是说会解析符号链接），然后按照上文描述的方式在`node_modules`目录中寻找依赖关系，所以可以使用如下的目录结构解决这个问题：

* `/usr/lib/node/foo/1.2.3/` - Contents of the `foo` package, version 1.2.3.
  `/usr/lib/node/foo/1.2.3/` - 包`foo`的1.2.3版本内容。
* `/usr/lib/node/bar/4.3.2/` - Contents of the `bar` package that `foo`
  depends on.
  `/usr/lib/node/bar/4.3.2/` - 包`foo`依赖的包`bar`的内容。
* `/usr/lib/node/foo/1.2.3/node_modules/bar` - Symbolic link to
  `/usr/lib/node/bar/4.3.2/`.
  `/usr/lib/node/foo/1.2.3/node_modules/bar` - 指向`/usr/lib/node/bar/4.3.2/`的符号链接。
* `/usr/lib/node/bar/4.3.2/node_modules/*` - Symbolic links to the packages
  that `bar` depends on.
  `/usr/lib/node/bar/4.3.2/node_modules/*` - 指向包`bar`所依赖的包的符号链接。

Thus, even if a cycle is encountered, or if there are dependency
conflicts, every module will be able to get a version of its dependency
that it can use.

因此即便存在循环依赖或依赖冲突，每个模块还是可以获得他所依赖的包的一个可用版本。

When the code in the `foo` package does `require('bar')`, it will get the
version that is symlinked into `/usr/lib/node/foo/1.2.3/node_modules/bar`.
Then, when the code in the `bar` package calls `require('quux')`, it'll get
the version that is symlinked into
`/usr/lib/node/bar/4.3.2/node_modules/quux`.

当包`foo`中的代码调用`require('bar')`，将获得符号链接`/usr/lib/node/foo/1.2.3/node_modules/bar`指向的版本。同样，当包`bar`中的代码调用`require('queue')`，降火的符号链接`/usr/lib/node/bar/4.3.2/node_modules/quux`指向的版本。

Furthermore, to make the module lookup process even more optimal, rather
than putting packages directly in `/usr/lib/node`, we could put them in
`/usr/lib/node_modules/<name>/<version>`.  Then node will not bother
looking for missing dependencies in `/usr/node_modules` or `/node_modules`.

为了进一步优化模块搜索过程，不要将包直接放在`/usr/lib/node`目录中，而是将它们放在`/usr/lib/node_modules/<name>/<version>`目录中。这样在依赖的包找不到的情况下，就不会一直寻找到`/usr/node_modules`目录或`/node_modules`目录中了。

In order to make modules available to the node REPL, it might be useful to
also add the `/usr/lib/node_modules` folder to the `$NODE_PATH` environment
variable.  Since the module lookups using `node_modules` folders are all
relative, and based on the real path of the files making the calls to
`require()`, the packages themselves can be anywhere.

为了使模块在node REPL中可用，你可能需要将`/usr/lib/node_modules`目录加入到`$NODE_PATH`环境变量中。由于在`node_modules`目录中搜索模块使用的是相对路径，基于调用`require()`的文件所在真实路径，因此包本身可以放在任何位置。
## Addons
##扩展插件


Addons are dynamically linked shared objects. They can provide glue to C and
C++ libraries. The API (at the moment) is rather complex, involving
knowledge of several libraries:

扩展插件（Addons）是一个动态连接分享对象。该对象提供了与C/C++类库的连接。由于涉及了多个类库导致了这类API目前比较繁杂，主要包括下述几个主要类库：

 - V8 JavaScript, a C++ library. Used for interfacing with JavaScript:
   creating objects, calling functions, etc.  Documented mostly in the
   `v8.h` header file (`deps/v8/include/v8.h` in the Node source tree).

 - V8 JavaScript，C++类库，作为JavaScript的接口类，主要用于创建对象、调用方法等功能。大部分功能在头文件`v8.h` (在node文件夹下的路径为`deps/v8/include/v8.h` )中有详细文档。

 - libev, C event loop library. Anytime one needs to wait for a file
   descriptor to become readable, wait for a timer, or wait for a signal to
   received one will need to interface with libev.  That is, if you perform
   any I/O, libev will need to be used.  Node uses the `EV_DEFAULT` event
   loop.  Documentation can be found [here](http://cvs.schmorp.de/libev/ev.html).

 - libev, 基于C的事件循环库。当需要等待文件描述（file descriptor）为可读时，等待定时器时，或者等待接受信号时，会需要调用libev库中的相关接口。也可以说，任何IO操作都需要调用libev库。Node使用EV_DEFAULT事件循环机制。在[这里](http://cvs.schmorp.de/libev/ev.html)可以查阅相关文档。

 - libeio, C thread pool library. Used to execute blocking POSIX system
   calls asynchronously. Mostly wrappers already exist for such calls, in
   `src/file.cc` so you will probably not need to use it. If you do need it,
   look at the header file `deps/libeio/eio.h`.

 - libeio，基于C的线程池库，用于异步阻塞POSIX系统呼叫。因为存在许多对这类呼叫的封装，所以在使用时，可能在 `src/file.cc`中不会经常调用libeio库。当使用者必须使用该类库时，其头文件路径为`deps/libeio/eio.h`。

 - Internal Node libraries. Most importantly is the `node::ObjectWrap`
   class which you will likely want to derive from.

 - 内部Node库。在该库中，大部分类是我们可能用于进行派生的`node::ObjectWrap`基类。

 - Others. Look in `deps/` for what else is available.

 - 其他的一些类库同样可以在`deps/` 中找到。

Node statically compiles all its dependencies into the executable. When
compiling your module, you don't need to worry about linking to any of these
libraries.

Node已将所有依赖关系静态地编译成可执行文件，因此我们在编译自己的组件时不需要担心和这些类库的连接问题。

To get started let's make a small Addon which does the following except in
C++:

除了使用C++以外，让我们着手编写一个Addon的小例子来达到如下效果：

    exports.hello = 'world';

To get started we create a file `hello.cc`:

首先我们需要创建一个`hello.cc`文件：


    #include <v8.h>

    using namespace v8;

    extern "C" void
    init (Handle<Object> target)
    {
      HandleScope scope;
      target->Set(String::New("hello"), String::New("world"));
    }

This source code needs to be built into `hello.node`, the binary Addon. To
do this we create a file called `wscript` which is python code and looks
like this:

这些源码会编译成一个二进制的Addon文件`hello.node`。为此我们用python编写如下的名为`wscript`的文件：

    srcdir = '.'
    blddir = 'build'
    VERSION = '0.0.1'

    def set_options(opt):
      opt.tool_options('compiler_cxx')

    def configure(conf):
      conf.check_tool('compiler_cxx')
      conf.check_tool('node_addon')

    def build(bld):
      obj = bld.new_task_gen('cxx', 'shlib', 'node_addon')
      obj.target = 'hello'
      obj.source = 'hello.cc'

Running `node-waf configure build` will create a file
`build/default/hello.node` which is our Addon.

运行`node-waf configure build`，我们就创建了一个Addon实例`build/default/hello.node`

`node-waf` is just [WAF](http://code.google.com/p/waf), the python-based build system. `node-waf` is
provided for the ease of users.

`node-waf`就是[WAF](http://code.google.com/p/waf),，一种基于python的编译系统，而`node-waf`更加易于使用。

All Node addons must export a function called `init` with this signature:

另外，在Node中任何的Addon必须使用输出一个含有如下签名的名为`init`的函数：

    extern 'C' void init (Handle<Object> target)

For the moment, that is all the documentation on addons. Please see
<http://github.com/ry/node_postgres> for a real example.

到此为止，这里呈现了关于addon的所有文档，另外，在<http://github.com/ry/node_postgres>中还提供了一个Addon的实例。
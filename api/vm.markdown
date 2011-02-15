## Executing JavaScript 执行JavaScript

You can access this module with:

可以通过如下方式访问此模块：

    var vm = require('vm');

JavaScript code can be compiled and run immediately or compiled, saved, and run later.

JavaScript代码可以编译并立即执行，也可以编译后存储，在以后执行。


### vm.runInThisContext(code, [filename])

`vm.runInThisContext()` compiles `code` as if it were loaded from `filename`,
runs it and returns the result. Running code does not have access to local scope. `filename` is optional.

`vm.runInThisContext()`编译并执行`code`代码，返回执行结果。运行的代码不具有访问本地作用域的权限。`code`代码被视作从`filename`文件中读取的，`filename`参数是可选的。

Example of using `vm.runInThisContext` and `eval` to run the same code:

使用`vm.runInThisContext`和`eval`两种方式运行同样代码的例子：

    var localVar = 123,
        usingscript, evaled,
        vm = require('vm');

    usingscript = vm.runInThisContext('localVar = 1;',
      'myfile.vm');
    console.log('localVar: ' + localVar + ', usingscript: ' +
      usingscript);
    evaled = eval('localVar = 1;');
    console.log('localVar: ' + localVar + ', evaled: ' +
      evaled);

    // localVar: 123, usingscript: 1
    // localVar: 1, evaled: 1

`vm.runInThisContext` does not have access to the local scope, so `localVar` is unchanged.
`eval` does have access to the local scope, so `localVar` is changed.

`vm.runInThisContext`不能访问本地作用域，所以`localVar`变量的值没有被修改。`eval`可以访问本地作用域，因此`localVar`变量的值被修改了。

In case of syntax error in `code`, `vm.runInThisContext` emits the syntax error to stderr
and throws.an exception.

当`code`代码中存在语法错误时，`vm.runInThisContext`将错误输出到标准错误，同时抛出一个异常。


### vm.runInNewContext(code, [sandbox], [filename])

`vm.runInNewContext` compiles `code` to run in `sandbox` as if it were loaded from `filename`,
then runs it and returns the result. Running code does not have access to local scope and
the object `sandbox` will be used as the global object for `code`.
`sandbox` and `filename` are optional.

`vm.runInNewContext`编译并在`沙箱`中执行`code`代码，返回执行结果。`sandbox`对象是代码可以访问的一个全局对象，代码不能访问本地作用域。`code`代码被视作从`filename`文件中读取的，`sandbox`参数和`filename`参数是可选的。

Example: compile and execute code that increments a global variable and sets a new one.
These globals are contained in the sandbox.

例子：编译并执行一段代码，这段代码增加一个全局变量的数值，并设置一个新的全局变量。这些全局变量均存在于沙箱中。

    var util = require('util'),
        vm = require('vm'),
        sandbox = {
          animal: 'cat',
          count: 2
        };

    vm.runInNewContext('count += 1; name = "kitty"', sandbox, 'myfile.vm');
    console.log(util.inspect(sandbox));

    // { animal: 'cat', count: 3, name: 'kitty' }

Note that running untrusted code is a tricky business requiring great care.  To prevent accidental
global variable leakage, `vm.runInNewContext` is quite useful, but safely running untrusted code
requires a separate process.

运行不可靠的代码是需要非常小心的危险操作。`vm.runInNewContext`对防止全局变量泄露非常有帮助，但最好还是在一个独立的进程中运行不可靠的代码。

In case of syntax error in `code`, `vm.runInThisContext` emits the syntax error to stderr
and throws an exception.

当`code`代码中存在语法错误时，`vm.runInThisContext`将错误输出到标准错误，同时抛出一个异常。


### vm.createScript(code, [filename])

`createScript` compiles `code` as if it were loaded from `filename`,
but does not run it. Instead, it returns a `vm.Script` object representing this compiled code.
This script can be run later many times using methods below.
The returned script is not bound to any global object.
It is bound before each run, just for that run. `filename` is optional.

`createScript`方法编译`code`代码但并不执行它。取而代之，此方法返回一个`vm.Script`对象以表示编译后的代码。之后，这个脚本可以使用下文所介绍的方法运行，并可运行多次。返回的脚本并不与任何全局对象绑定，只有在实际运行时才会与全局对象绑定，而且绑定只对一次运行有效。`code`代码被视作从`filename`文件中读取的，`filename`参数是可选的。

In case of syntax error in `code`, `createScript` prints the syntax error to stderr
and throws an exception.

当`code`代码中存在语法错误时，`createScript` 将错误输出到标准错误，同时抛出一个异常。


### script.runInThisContext()

Similar to `vm.runInThisContext` but a method of a precompiled `Script` object.
`script.runInThisContext` runs the code of `script` and returns the result.
Running code does not have access to local scope, but does have access to the `global` object
(v8: in actual context).

这是预编译的`Script`对象的一个方法，其作用与`vm.runInThisContext`类似。`script.runInThisContext`运行`script`对象的代码并返回结果。运行的代码不能访问本地作用域，但可以访问（v8：实际上下文中的）`global`全局对象。

Example of using `script.runInThisContext` to compile code once and run it multiple times:

使用`script.runInThisContext`一次编译多次执行代码的例子：

    var vm = require('vm');

    globalVar = 0;

    var script = vm.createScript('globalVar += 1', 'myfile.vm');

    for (var i = 0; i < 1000 ; i += 1) {
      script.runInThisContext();
    }

    console.log(globalVar);

    // 1000


### script.runInNewContext([sandbox])

Similar to `vm.runInNewContext` a method of a precompiled `Script` object.
`script.runInNewContext` runs the code of `script` with `sandbox` as the global object and returns the result.
Running code does not have access to local scope. `sandbox` is optional.

这是预编译的`Script`对象的一个方法，其作用与`vm.runInNewContext`类似。`script.runInNewContext`将`sandbox`作为全局对象，运行`script`对象的代码并返回结果。运行的代码不能访问本地作用域。`sandbox`是可选参数。

Example: compile code that increments a global variable and sets one, then execute this code multiple times.
These globals are contained in the sandbox.

例子：编译一段代码并执行多次，这段代码增加一个全局变量的数值，并设置一个新的全局变量。这些全局变量均存在于沙箱中。

    var util = require('util'),
        vm = require('vm'),
        sandbox = {
          animal: 'cat',
          count: 2
        };

    var script = vm.createScript('count += 1; name = "kitty"', 'myfile.vm');

    for (var i = 0; i < 10 ; i += 1) {
      script.runInNewContext(sandbox);
    }

    console.log(util.inspect(sandbox));

    // { animal: 'cat', count: 12, name: 'kitty' }

Note that running untrusted code is a tricky business requiring great care.  To prevent accidental
global variable leakage, `script.runInNewContext` is quite useful, but safely running untrusted code
requires a separate process.

运行不可靠的代码是需要非常小心的危险操作。`script.runInNewContext`对防止全局变量泄露非常有帮助，但最好还是在一个独立的进程中运行不可靠的代码。
## Timers 定时器

### setTimeout(callback, delay, [arg], [...])

To schedule execution of `callback` after `delay` milliseconds. Returns a
`timeoutId` for possible use with `clearTimeout()`. Optionally, you can
also pass arguments to the callback.

设定一个`delay`毫秒后执行`callback`回调函数的计划。返回值`timeoutId`可被用于`clearTimeout()`。可以设定要传递给回调函数的参数。

### clearTimeout(timeoutId)

Prevents a timeout from triggering.

清除定时器，阻止指定的timeout（超时）定时器被触发。

### setInterval(callback, delay, [arg], [...])

To schedule the repeated execution of `callback` every `delay` milliseconds.
Returns a `intervalId` for possible use with `clearInterval()`. Optionally,
you can also pass arguments to the callback.

设定一个每`delay`毫秒重复执行`callback`回调函数的计划。返回值`intervalId`可被用于`clearInterval()`。可以设定要传递给回调函数的参数。

### clearInterval(intervalId)

Stops a interval from triggering.

清除定时器，阻止指定的interval（间隔）定时器被触发。
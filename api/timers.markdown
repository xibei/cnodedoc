## Timers 定时器

### setTimeout(callback, delay, [arg], [...])

To schedule execution of `callback` after `delay` milliseconds. Returns a
`timeoutId` for possible use with `clearTimeout()`. Optionally, you can
also pass arguments to the callback.

设定一个`delay`毫秒后执行`callback`回调函数的计划。返回值`timeoutId`可能
会被用于`clearTimeout()`。可以选择传递参数给回调函数。

### clearTimeout(timeoutId)

Prevents a timeout from triggering.

防止一个超时定时器被触发。

### setInterval(callback, delay, [arg], [...])

To schedule the repeated execution of `callback` every `delay` milliseconds.
Returns a `intervalId` for possible use with `clearInterval()`. Optionally,
you can also pass arguments to the callback.

设定一个每`delay`毫秒重复执行`callback`回调函数的计划。返回值`intervalId`
可能会被用于`clearInterval()`。可以选择传递参数给回调函数。

### clearInterval(intervalId)

Stops a interval from triggering.

防止一个间隔定时器被触发。
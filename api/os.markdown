## os Module os 模块

Use `require('os')` to access this module.

可以通过`require('os')`访问这个os 模块。

### os.hostname()

Returns the hostname of the operating system.

该方法返回当前操作系统的主机名.

### os.type()

Returns the operating system name.

该方法返回当前操作系统名称

### os.release()

Returns the operating system release.

### os.release()

该方法返回当前操作系统的发行版本

### os.uptime()

Returns the system uptime in seconds.

该方法返回当前系统的正常运行时间，时间以秒为单位

### os.loadavg()

Returns an array containing the 1, 5, and 15 minute load averages.


该方法返回一个数组，该数组存储着系统1分钟,5分钟,以及15分钟时间长度的负载均值

### os.totalmem()

Returns the total amount of system memory in bytes.

返回系统存储空间总值，该值以字节（ byte）为单位

### os.freemem()

Returns the amount of free system memory in bytes.

返回系统存储的剩余空间，该值以字节（ byte）为单位

### os.cpus()

Returns an array of objects containing information about each CPU/core installed: model, speed (in MHz), and times (an object containing the number of 

该方法返回一个对象数组，该数组包含了关于系统每个CPU/CPU核心的信息：模式（Mode），速度（以MHz为单位）(Speed)，以及时间（一个包含CPU资

CPU ticks spent in: user, nice, sys, idle, and irq).

源使用率的对象，包括用户(User)，Nice，系统(Sys)，以及中断请求（IRQ）等几个方面的参数。


Example inspection of os.cpus:

os.cpus以一个示例如下:

    [ { model: 'Intel(R) Core(TM) i7 CPU         860  @ 2.80GHz',
        speed: 2926,
        times:
         { user: 252020,
           nice: 0,
           sys: 30340,
           idle: 1070356870,
           irq: 0 } },
      { model: 'Intel(R) Core(TM) i7 CPU         860  @ 2.80GHz',
        speed: 2926,
        times:
         { user: 306960,
           nice: 0,
           sys: 26980,
           idle: 1071569080,
           irq: 0 } },
      { model: 'Intel(R) Core(TM) i7 CPU         860  @ 2.80GHz',
        speed: 2926,
        times:
         { user: 248450,
           nice: 0,
           sys: 21750,
           idle: 1070919370,
           irq: 0 } },
      { model: 'Intel(R) Core(TM) i7 CPU         860  @ 2.80GHz',
        speed: 2926,
        times:
         { user: 256880,
           nice: 0,
           sys: 19430,
           idle: 1070905480,
           irq: 20 } },
      { model: 'Intel(R) Core(TM) i7 CPU         860  @ 2.80GHz',
        speed: 2926,
        times:
         { user: 511580,
           nice: 20,
           sys: 40900,
           idle: 1070842510,
           irq: 0 } },
      { model: 'Intel(R) Core(TM) i7 CPU         860  @ 2.80GHz',
        speed: 2926,
        times:
         { user: 291660,
           nice: 0,
           sys: 34360,
           idle: 1070888000,
           irq: 10 } },
      { model: 'Intel(R) Core(TM) i7 CPU         860  @ 2.80GHz',
        speed: 2926,
        times:
         { user: 308260,
           nice: 0,
           sys: 55410,
           idle: 1071129970,
           irq: 880 } },
      { model: 'Intel(R) Core(TM) i7 CPU         860  @ 2.80GHz',
        speed: 2926,
        times:
         { user: 266450,
           nice: 1480,
           sys: 34920,
           idle: 1072572010,
           irq: 30 } } ]

---
title: log(winston)
date: 2017-09-28 16:49:57
tags:
---
[github-winston](https://github.com/winstonjs/winston)

文档其实感觉没看太懂，先不管了，反正就这么用：
``` js
const winston = require('winston')

// 'test' 其实就是个命名空间了 
winston.loggers.add('test', {
  console: {
    level: 'silly', // { error: 0, warn: 1, info: 2, verbose: 3, debug: 4, silly: 5 }
    colorize: true, // true 上颜色
    label: 'test...', // 前缀(一般写模块名吧)
  },
  file: {
    filename: 'somefile.log' // log的输出文件
  },
})
const logger = winston.loggers.get('test')

logger.error(`error`)
logger.warn(`warn`)
logger.info(`info`)
logger.verbose(`verbose`)
logger.debug('debug')
logger.silly('silly')
```
![images](http://oifogbmox.bkt.clouddn.com/170928-1.png)
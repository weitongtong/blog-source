---
title: 使用git钩子做eslint校验
date: 2017-09-21 17:48:59
tags:
---
[demo地址](https://github.com/weitongtong/git-eslint-hook)

## git hook

在 git 中，执行 commit 、 push 等特殊事件时，都会触发执行一个或多个任意的 shell 脚本，我们称之为git钩子，它存放在 .git/hooks 目录下，我们可以看到目录下有 commit-msg.sample 、pre-commit.sample 等文件，这些都是案例文件，不会执行，要想执行的话把后面的 .sample 后缀去掉就可以了。

钩子从执行顺序上分两类：
* 前置（pre）钩子，在动作完成前调用
* 后置（post）钩子，在动作完成后执行

通常情况下，**如果前置钩子一非零状态下退出，那么git动作就会终止**，这样我们就可以在commit前对提交的内容做一些校验，如果不符合规定就不让提交。

> 钩子默认是不会继承的，也就是说如果你从仓库clone下来的版本库是没有这些钩子的。

## pre-commit

.git/hooks 目录下的都是命令文件，我是看不懂的，咋办？
别急 看这个 [pre-commit](https://github.com/observing/pre-commit)
然后，我们就不用手动去修改 .git/hooks/commit-msg 文件了

## eslint
[eslint](https://github.com/eslint/eslint)
``` bash
$ sudo npm i eslint -g
$ mkdir demo && cd demo
$ eslint --init // 会自动创建 .eslintrc.js
$ touch .eslintignore // 创建 .eslintignore
$ eslint index.js // eslint检测 index.js 文件
```

最后，看重头戏，package.json配置：
``` json
{
  "name": "eslint-demo",
  "version": "1.0.0",
  "description": "",
  "main": ".eslintrc.js",
  "scripts": {
    "test": "node_modules/.bin/eslint src"
  },
  "pre-commit": [
    "test"
  ],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "eslint": "^3.10.2",
    "pre-commit": "^1.2.2"
  }
}
```
当我们 commit 的时候 就会去执行 npm run test ，如果有异常就会终止 git 的操作了。暂时先到这吧，后续再补充。。。
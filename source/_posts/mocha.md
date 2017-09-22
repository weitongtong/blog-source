---
title: mocha(unit testing)
date: 2017-09-22 11:18:48
tags:
---
[mocha-demo](https://github.com/weitongtong/test-demo/tree/master/mocha-demo)
[mochajs.org](https://mochajs.org)

(1) 安装 Mocha 和 Chai
``` bash
$ cd mocha-demo
$ npm i mocha -D
$ npm i chai -D
```
> chai 为断言库 可任意选择

(2) 打开我们要测试的脚本 src/add.js
``` javascript
module.exports = function add(x, y) {
  return x + y
}
```

(3) 编写一个测试脚本 src/add.test.js
``` javascript
const add = require('./add.js')
const assert = require('assert') // node原生自带
const expect = require('chai').expect // chai模块

describe('测试add方法', () => {
  it('1 + 1 应该等于 2', () => {
    expect(add(1, 1)).to.be.equal(2)
  })
  it('3 + (-3) 应该等于 0', () => {
    assert.equal(0, add(3, -3))
  })
})
```
(4) package.json 中配置 script
``` json
{
  "name": "mocha-demo",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "mocha src/*.test.js"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "chai": "^4.1.2",
    "mocha": "^3.5.3"
  }
}
```

测试脚本与所要测试的源码脚本同名，但是后缀名为`.test.js`（表示测试）或者`.spec.js`（表示规格）。比如，`add.js`的测试脚本名字就是`add.test.js`。

测试脚本里面应该包括一个或多个`describe`块，每个`describe`块应该包括一个或多个`it`块。

`describe`块称为"**测试套件**"`（test suite）`，表示一组相关的测试。它是一个函数，第一个参数是测试套件的名称（"加法函数的测试"），第二个参数是一个实际执行的函数。

`it`块称为"**测试用例**"`（test case）`，**表示一个单独的测试，是测试的最小单位。**它也是一个函数，第一个参数是测试用例的名称（"1 加 1 应该等于 2"），第二个参数是一个实际执行的函数。

上面的测试脚本里面，有一句断言:
``` javascript
expect(add(1, 1)).to.be.equal(2)
```
所谓"断言"，就是判断源码的实际执行结果与预期结果是否一致，如果不一致就抛出一个错误。上面这句断言的意思是，调用`add(1, 1)`，结果应该等于2。

所有的测试用例（`it`块）都应该含有一句或多句的断言。它是编写测试用例的关键。断言功能由断言库来实现，`Mocha`本身不带断言库，所以必须先引入断言库。
``` javascript
const expect = require('chai').expect
```
断言库有很多种，Mocha并不限制使用哪一种。上面代码引入的断言库是chai，并且指定使用它的expect断言风格。

暂时这么多，后续补充。。。
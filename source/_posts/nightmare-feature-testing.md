---
title: Nightmare(feature testing)
date: 2017-09-22 13:14:25
tags:
---
[nightmare-demo](https://github.com/weitongtong/test-demo/tree/master/nightmare-demo)

feature testing: 功能测试

(1) 安装 `nightmare`  `mocha`  `chai` `http-server`
  ``` bash
  # 安装 Electron
  $ env ELECTRON_MIRROR=https://npm.taobao.org/mirrors/electron/ npm install
  $ npm i nightmare mocha chai -D
  $ npm i http-server -D
  ```
  > 注意，Nightmare 会先安装 Electron，而 Electron 的安装需要下载境外的包，有时会连不上，导致安装失败。所以，这里先设置了环境变量，指定使用国内的 Electron 源，然后才执行安装命令。

(2) 查看需要测试的功能页面
  index.html
  ``` html
  <!DOCTYPE html>
  <html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
      #demo{
        color: red;
      }
    </style>
  </head>
  <body>
    <div id="demo">hello world</div>
    <script>
      document.querySelector('#demo').addEventListener('click', function(event) {
        this.textContent = 'hello wtt'
        this.style.color = 'blue'
      })
    </script>
  </body>
  </html>
  ```

  server.js
  ``` javascript
  const httpServer = require('http-server')
  const server = httpServer.createServer()

  server.listen(8888)

  // If Node.js is spawned with an IPC channel, the process.send() method can be used to send messages to the parent process. Messages will be received as a 'message' event on the parent's ChildProcess object.
  // If Node.js was not spawned with an IPC channel, process.send() will be undefined.
  if (process.send) {
    process.send('listening')
  }
  ```

  (3) 编写测试脚本 test.js
  ``` javascript
  const Nightmare = require('nightmare')
  const expect = require('chai').expect
  const fork = require('child_process').fork

  describe('test index.html', () => {
    let child

    // 测试开始前执行
    before((done) => {
      // 新建了一个子进程来启动 HTTP 服务器
      child = fork('server.js')
      child.on('message', (msg) => {
        if (msg === 'listening') {
          // done 是 mocha 提供的一个函数，用来表示异步操作完成。
          // 如果不调用 done，mocha 就会认为异步操作没有结束，不会往下执行，从而导致超时错误。
          done()
        }
      })
    })

    // 测试结束后执行
    after(() => {
      // 关闭子进程
      child.kill()
    })

    it('点击后标题改变', (done) => {
      const nightmare = Nightmare({ show: true })
      nightmare
        .goto('http://127.0.0.1:8888/index.html')
        .click('#demo')
        .wait(1000)
        .evaluate(() => {
          const dom = document.querySelector('#demo')
          return {
            text: dom.textContent,
            color: window.getComputedStyle(dom).color
          }
        })
        .end()
        .then(({ text, color }) => {
          expect(text).to.equal('hello wtt1')
          expect(color).to.equal('rgb(0, 0, 255)')
          done()
        })
        .catch((err) => {
          console.log(err)
          done()
        })
    })

  })
  ```

  (4) package.json
  ``` json
  {
    "name": "nightmare-demo",
    "version": "1.0.0",
    "description": "",
    "main": "server.js",
    "scripts": {
      "test": "mocha -t 15000 test.js",
      "start": "node server.js"
    },
    "author": "",
    "license": "ISC",
    "devDependencies": {
      "chai": "^4.1.2",
      "http-server": "^0.10.0",
      "nightmare": "^2.10.0"
    }
  }
  ```
  ``` bash
  $ npm run test
  ```
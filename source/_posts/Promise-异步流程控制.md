---
title: Promise 异步流程控制
date: 2017-09-29 15:21:17
tags:
---
问题：
> 网页中预加载20张图片资源，分布加载，一次加载10张，两次完成，怎么控制图片请求的并发，怎么感知当前异步请求是否完成？

html代码如下：
```html
<body>
  <div class="wrap">
    <div class="loading">正在加载。。。</div>
    <div class="imgs"></div>
  </div>
</body>
```

#### 单一请求
最简单的，就是将异步一个个来处理，转为一个类似同步的方式来处理。
先来简单的实现一个单个 Image 来加载的 thenable 函数和一个处理函数返回结果的函数
``` js
function loadImg(url) {
  return new Promise((resolve, reject) => {
    const img = new Image()
    img.onload = function() {
      resolve(img)
    }
    img.onerror = reject
    img.src = url
  })
}
```
异步转同步的解决思路是：当第一个 `loadImg(urls[0])` 完成后再调用 `loadImg(urls[1])`，依次往下。

这里通过 Promise chain (其实就是一直then()下去)来实现：
``` js
let promise = Promise.resolve() // 用一个中间变量来存储当前的 promise，就像链表的游标一样。
for (let i = 0; i < urls.length; i++) {
  promise = promise
    .then(() => loadImg(urls[i]))
    .then(addToHtml)
}
```
promise 变量就像一个迭代器，不断指向最新的放回的 Promise，进一步使用 reduce 来简化：
``` js
urls.reduce((promise, url) => {
  return promise
    .then(() => loadImg(url))
    .then(addToHtml)
}, Promise.resolve())
```
当然，我们也可以改成递归的形式来实现：
```js
function syncLoad(index) {
  if (index >= urls.length) return
  loadImg(urls[index])
    .then(img => {
      // do something
      addToHtml(img)
      syncLoad(index + 1)
    })
}
// 调用
syncLoad(0)
```
好了 一个简单的异步转同步的实现方式就已经完成了。但还需要在递归结束的时候，隐藏loading。
```js
function syncLoad(index) {
  if (index >= urls.length) return Promise.resolve()
  return loadImg(urls[index])
    .then(img => {
      addToHtml(img)
      return syncLoad(index + 1)
    })
}
// 调用
syncLoad(0)
  .then(() => {
    document.querySelector('.loading').style.display = 'none'
  })
```

现在我们再来完善一下这个函数，让它更加通用。
```js
/**
 * @param {Function} fn 异步函数 - loadImg
 * @param {Array} arr 异步函数需要的参数 - urls
 * @param {Function} handler 异步函数的回调 - addToHtml
 */
function syncLoad(fn, arr, handler) {
  if (typeof fn !== 'function') throw TypeError('第一个参数必须是函数')
  if (!Array.isArray(arr)) throw TypeError('第二个参数必须是数组')
  handler = typeof handler === 'function' ? handler : function() {}

  const errors = []

  function load(index) {
    if (index >= arr.length) {
      return errors.length > 0 ? Promise.reject(errors) : Promise.resolve()
    }
    return fn(arr[index])
      .then(data => {
        handler(data)
      })
      .catch(err => {
        console.log(err)
        errors.push(arr[index])
        return load(index + 1)
      })
      .then(() => {
        return load(index + 1)
      })
  }

  return load(0)
}

// 调用
syncLoad(loadImg, urls, addToHtml)
  .then(() => {
    document.querySelector('.loading').style.display = 'none'
  })
  .catch(console.log)
```

当然这种异步转同步的方式在这一个例子中并不是最好的做法，但当有何时的业务场景时，这是很常见的解决方案。

#### 并发请求
使用 Promise.all

> Promise.all(iterable) 方法指当所有在可迭代参数中的 promises 已完成，或者第一个传递的 promise（指 reject）失败时，返回 promise。

``` js
const promises = urls.map(loadImg)
Promise.all(promises)
  .then(imgs => {
    imgs.forEach(addToHtml)
    document.querySelector('.loading').style.display = 'none'
  })
  .catch(err => {
    console.error(err, 'Promise.all 当其中一个出现错误，就会reject。')
  })
```
#### 并发请求，按顺序处理结果
webapp 里常用的资源预加载，可能加载的是 20 张逐帧图片，当网络出现问题， 20 张图难免会有一两张请求失败，如果失败后，直接抛弃其他被 resolve 的返回结果，似乎有点不妥，我们只要知道哪些图片出错了，把出错的图片再做一次请求或着用占位图补上就好。
上节中的代码 `const promises = urls.map(loadImg)` 运行后，全部都图片请求都已经发出去了，我们只要按顺序挨个处理 promises 这个数组中的 Promise 实例就好了。
```js
const promises = urls.map(loadImg)
let task = Promise.resolve()
for (let i = 0; i < promises.length; i++) {
  task = task.then(() => promises[i]).then(addToHtml)
}
```
reduce 版本
```js
const promises = urls.map(loadImg)
promises.reduce((task, imgPromise) => {
  return task().then(() => imgPromise).then(addToHtml)
}, Promise.resolve())
```

#### 控制最大并发数
现在我们来试着完成一下上面的问题，这个其实都不需要控制最大并发数。
20张图，分两次加载，那用两个 Promise.all 不就解决了？但是用 Promise.all 没办法侦听到每一张图片加载完成的事件。而用上一节的方法，我们既能并发请求，又能按顺序响应图片加载完成的事件。
```js
let index = 0
const step1 = [], step2 = []

while(index < 10) {
  step1.push(loadImg(`./images/pic/${index}.jpg`))
  index += 1
}

step1.reduce((task, imgPromise, i) => {
  return task
    .then(() => imgPromise)
    .then(() => {
      console.log(`第 ${i + 1} 张图片加载完成.`)
    })
}, Promise.resolve())
  .then(() => {
    console.log('>> 前面10张已经加载完！')
  })
  .then(() => {
    while(index < 20) {
      step2.push(loadImg(`./images/pic/${index}.jpg`))
      index += 1
    }
    return step2.reduce((task, imgPromise, i) => {
      return task
        .then(() => imgPromise)
        .then(() => {
          console.log(`第 ${i + 11} 张图片加载完成.`)
        })
    }, Promise.resolve())
  })
  .then(() => {
    console.log('>> 后面10张已经加载完')
  })
```
抽象一下代码，写一个通用的方法:
``` js
function stepLoad (urls, handler, stepNum) {
  const createPromises = function (now, stepNum) {
    let last = Math.min(stepNum + now, urls.length)
    return urls.slice(now, last).map(handler)
  }
  let step = Promise.resolve()
  for (let i = 0; i < urls.length; i += stepNum) {
    step = step
      .then(() => {
        let promises = createPromises(i, stepNum)
        return promises.reduce((task, imgPromise, index) => {
          return task
            .then(() => imgPromise)
            .then(() => {
              console.log(`第 ${index + 1 + i} 张图片加载完成.`)
            })
        }, Promise.resolve())
      })
      .then(() => {
        let current = Math.min(i + stepNum, urls.length)
        console.log(`>> 总共${current}张已经加载完！`)
      })
  }
  return step
}
```
但上面的实现和我们说的最大并发数控制没什么关系啊，最大并发数控制是指：当加载 20 张图片加载的时候，先并发请求 10 张图片，当一张图片加载完成后，又会继续发起一张图片的请求，让并发数保持在 10 个，直到需要加载的图片都全部发起请求。这个在写爬虫中可以说是比较常见的使用场景了。
实现方式：使用递归
假设我们的最大并发数是 4 ，这种方法的主要思想是相当于 4 个单一请求的 Promise 异步任务在同时运行运行，4 个单一请求不断递归取图片 URL 数组中的 URL 发起请求，直到 URL 全部取完，最后再使用 Promise.all 来处理最后还在请求中的异步任务，我们复用第二节递归版本的思路来实现这个功能：
```js
function limitLoad (urls, handler, limit) {
  const sequence = [].concat(urls) // 对数组做一个拷贝
  let count = 0
  const promises = []

  const load = function () {
    if (sequence.length <= 0 || count > limit) return 
    count += 1
    console.log(`当前并发数: ${count}`)
    return handler(sequence.shift())
      .catch(err => {
        console.error(err)
      })
      .then(() => {
        count -= 1
        console.log(`当前并发数：${count}`)
      })
      .then(() => load())
  }

  for(let i = 0; i < limit && i < sequence.length; i++){
    promises.push(load())
  }
  return Promise.all(promises)
}
```

[link](https://juejin.im/post/59cdb6526fb9a00a4e67c7fb)
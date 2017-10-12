---
title: es6-proxy
date: 2017-10-06 08:54:41
tags:
---
Proxy 用于修改某些操作的默认行为，等同于在语言层面做出修改，所以属于一种`元编程`，即对编程语言进行编程。

Proxy 可以理解成，在目标对象之前架设一层 `拦截`，外界对该对象进行访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。Proxy 在这里可以译为`代理器`。
<!--
receiver: 接收器
reflect: 反射
-->
```js
var obj = new Proxy({}, {
  get: function(target, key, receiver) {
    console.log(`getting ${key}!`)
    return Reflect.get(target, key, receiver)
  }
  set: function(target, key, value, receiver) {
    console.log(`setting ${key}!`)
    return Reflect.set(target, key, value, receiver)
  }
})
obj.count = 1 // => setting count!
++obj.count
// => getting count!
// => setting count!
// 2

```
上面代码对一个空对象架设了一层拦截，重定义了属性的读取（get）和设置（set）行为。
Proxy 实际上重载（overload）了点运算符，即用自己的定义覆盖了语言的原始定义。

ES6 原生提供 Proxy 构造函数，用来生成 Proxy 实例。
```js
// target参数表示要拦截的目标对象，handler参数也是一个对象，用来定制拦截行为。
var proxy = new Proxy(target, handler)
```
```js
var proxy = new Proxy({}, {
  get: function(target, property) {
    // target: 目标对象 property: 对象属性
    console.log(target, property)
    return 1
  }
})
proxy.name // => 1
proxy.aa // => 1
```
```js
var handler = {
  get: function(target, name) {
    if (name === 'prototype') {
      return Object.prototype
    }
    return 'Hello, ' + name
  },

  apply: function(target, thisBinding, args) {
    return args[0]
  },

  construct: function(target, args) {
    return { value: args[1] }
  }
}

var fproxy = new Proxy(function(x, y) {
  return x + y
}, handler)

fproxy(1, 2) // => 1 // 执行的是 apply
new fproxy(1, 2) // => {value: 2} // 执行的是 construct
fproxy.prototype === Object.prototype // => true // get中设置了
fproxy.foo === "Hello, foo" // => true
```
对于可以设置，但没有设置拦截的操作，则直接落在目标对象上，按照原先的方式产生结果。

---

Proxy 支持的拦截操作一览：
* get(target, propKey, receiver): 拦截对象属性的读取。
* set(target, propKey, value, receiver): 拦截对象属性的设置。
* has(target, propKey): 拦截 propKey in proxy 的操作，返回一个Boolean值。
* deleteProperty(target, propKey): 拦截 delete proxy[propKey] 的操作，返回一个Boolean值。
* ownKeys(target): 拦截 Object.getOwnPropertyNames(proxy)、Object.getOwnPropertySymbols(proxy)、Object.keys(proxy)，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而 Object.keys() 的返回结果仅包括目标对象自身的可遍历属性。
* getOwnPropertyDescriptor(target, propKey): 拦截 Object.getOwnPropertyDescriptor(proxy, propKey)，返回属性的描述对象。
* defineProperty(target, propKey, propDesc): 拦截 Object.defineProperty(proxy, propKey, propDesc)、Object.defineProperties(proxy, propDescs)，返回一个Boolean值。
* preventExtensions(target): 拦截 Object.preventExtensions(proxy)，返回一个Boolean值。
* getPrototypeOf(target): 拦截 Object.getPrototypeOf(proxy)，返回一个对象。
* isExtensible(target): 拦截 Object.isExtensible(proxy)，返回一个布尔值。
* setPrototypeOf(target, proto): 拦截Object.setPrototypeOf(proxy, proto)，返回一个布尔值。如果目标对象是函数，那么还有两种额外操作可以拦截。
* apply(target, object, args): 拦截 Proxy 实例作为函数调用的操作，比如proxy(...args)、proxy.call(object, ...args)、proxy.apply(...)。
* construct(target, args): 拦截 Proxy 实例作为构造函数调用的操作，比如new proxy(...args)。

```js
function createArray(...elements) {
  let handler = {
    get(target, propKey, receiver) {
      let index = Number(propKey);
      if (index < 0) {
        propKey = String(target.length + index);
      }
      console.log(target)
      return Reflect.get(target, propKey, receiver);
    }
  };

  let target = elements.slice(0)
  return new Proxy(target, handler);
}

let arr = createArray('a', 'b', 'c');
console.log(arr[-1]) // => 'c'
```

利用 Proxy，可以将读取属性的操作（get），转变为执行某个函数，从而实现属性的链式操作：
```js
var pipe = (function () {
  return function (value) {
    var funcStack = [];
    var oproxy = new Proxy({} , {
      get : function (pipeObject, fnName) {
        if (fnName === 'get') {
          console.log(funcStack)
          return funcStack.reduce(function (val, fn) {
            return fn(val);
          },value);
        }
        funcStack.push(global[fnName]);
        return oproxy;
      }
    });

    return oproxy;
  }
}());

global.double = n => n * 2;
global.pow    = n => n * n;
global.reverseInt = n => n.toString().split("").reverse().join("") | 0;

console.log(pipe(3).double.pow.reverseInt.get) // => 63
```

具体实例看：[doc](http://es6.ruanyifeng.com/#docs/proxy)


<div class="to-be-continued1"></div>
<!-- more -->
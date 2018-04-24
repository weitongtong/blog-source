---
title: interview-other
date: 2018-03-21 15:20:16
tags:
---
* let和var区别
  var 作用域为该语句所在的函数内，且存在变量提升
  let 作用域为该语句所在的代码块内，不存在变量提升，且不允许重复声明

  为什么var可以重复声明？
  var a;
  a = 2
  编译器在执行的时候（从左往右）遇到 var a; 会询问当前作用域中是否已经存在叫 a 的变量
  如果不存在，则让作用域声明一个新的变量 a , 若已存在，则忽略 var 继续向下编译
  当遇到 a = 2 时同样会询问在当前作用域下是否有变量 a ，若存在，则 a 赋值为 2 （由于
  第一步编译器忽略重复声明的 var，且作用域中已经有 a ，所以重复声明会发生值得覆盖而并不会报错）
  若不存在，则顺着作用域链向上查找，若最终找到了变量a则将其赋值2，若没有找到，则让作用域声明一
  个变量a并赋值为2。

* CommonJs 中的 require/exports 和 ES6 中的 import/export 区别
  主要是运行机制的不同
  1. CommonJs 模块的重要特性是加载时执行，即脚本代码在 require 的时候，就会全部执行。
  一旦出现某个模块被 ‘循环加载‘，就只输出已经执行部分，还未执行的部分不会输出
  a依赖b b依赖a 当a执行跳到b，在b运行时到该require(a)语句时，它返回的只是a已经执行过的那部分结果的返回（module.exports）
  http://www.ruanyifeng.com/blog/2015/11/circular-dependency.html
  2. ES6 模块是动态引用，不存在缓存值的问题。通过import去加载的时候，不会去执行模块，而只是生成一个引用。
    多个模块 同时 import 了同一个文件（export了一个对象），改文件的代码只会执行一次，所有拿到的都是同一个对象。

  3. import/export 最终都是编译为 require/exports 来执行的。


* 怎么判断两个对象是否相等
  JSON.stringify(obj)==JSON.stringify(obj2);//true
  但是如果属性的位置不一致就不行了

* 项目做过哪些性能优化
  1. 减少 http 请求
  2. 减少 dns 查询
  3. 使用 cdn
  4. 避免重定向
  5. 图片懒加载
  6. 减少 dom 元素数量
  7. 减少 dom 操作
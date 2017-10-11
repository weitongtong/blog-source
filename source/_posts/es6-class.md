---
title: es6-class
date: 2017-10-11 14:46:53
tags:
---
#### class基本语法

#### class继承

```js
class Point {
}

class ColorPoint extends Point{
  constructor(x, y, color) {
    super(x, y) // 调用父类的 constructor(x, y)
    this.color = color
  }
  toString() {
    return this.color + ' ' + super.toString() // 调用父类的 toString()
  }
}
```
这里的 super 表示 父类的构造函数，用来新建父类的 this 对象。
子类必须在 constructor 方法中调用 super 方法，否则新建实例时会报错。这是因为子类没有自己的 this 对象，而是继承父类的 this 对象，然后对其进行加工。如果不调用 super 方法，子类就得不到 this 对象。

<!-- more -->

> ES5 的继承，实质是先创造子类的实例对象 this ，然后再将父类的方法添加到 this 上面 (`Parent.apply(this)`)。ES6 的继承机制完全不同，实质是先创造父类的实例对象 this (所以必须先调用`super`方法)，然后再用子类的构造函数修改`this`。

Object.getPrototypeOf方法：
```js
Object.getPrototypeOf(ColorPoint) === Point // true
```

super关键字
既可以当作函数使用，也可以当作对象使用。
(1)super 作为函数调用时，代表父类的构造函数
```js
class A {
  constructor() {
    console.log(new.target.name) // new.target 指向当前正在执行的函数
  }
}
class B extends A{
  constructor() {
    super()
  }
}
new A() // A
new B() // B
```
super 虽然代表了父类 A 的构造函数，但返回的是子类 B 的实例，即 super 内部的 this 指的是 B，因此 super() 在这里相当于 A.prototype.constructor.call(this)

> 作为函数时，super() 只能在子类的 构造函数中使用。

(2) super 作为对象时：
* 在普通方法中，指向父类的原型对象。
* 在静态方法中，指向父类。

```js
class A {
  constructor() {
    this.p = 2
  }
}
A.prototype.q = 3
class B extends A {
  get m() {
    return super.p
  }
  get n() {
    return super.q
  }
}
let b = new B()
b.m // undefined
b.n // 3
```
由于 super 指向父类的原型对象，所以定义在父实例上的方法或属性，是无法通过 super 调用到的。

```js
class A {
  constructor() {
    this.x = 1
  }
}
class B extends A {
  constructor() {
    super();
    this.x = 2
    super.x = 3 // super就是this
    console.log(super.x) // => undefined  // 读取的是A.prototype.x
    console.log(this.x) // => 3
  }
}
let b = new B();
```

prototype属性
* 子类的`__proto__`属性，表示构造函数的继承，总是指向父类。
* 子类prototype属性的`__proto__`属性，表示方法的继承，总是指向父类的prototype属性。

```js
class A {
}
class B extends A {
}
B.__proto__ === A // true //目的是为了继承静态属性和方法
B.prototype.__proto__ === A.prototype // true //可以理解为B的原型(B.prototype)指向A类的一个实例
```

看下3种比较特殊的情况，其实都不难理解：
```js
class A extends Object {
}
A.__proto__ === Object // true
A.prototype.__proto__ === Object.prototype // true
```
```js
class A {
}
A.__proto__ === Function.prototype // true
A.prototype.__proto__ === Object.prototype // true
```
```js
class A extends null {
}
A.__proto__ === Function.prototype // true
A.prototype.__proto__ === undefined // true
```


<div class="to-be-continued"></div>
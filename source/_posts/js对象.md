---
title: js对象
date: 2017-10-10 14:02:33
tags:
---
```js
function Person(name, age) {
  this.name = name
  this.age = age
  this.sayName = function() {
    alert(this.name)
  }
}
var p1 = new Person('Bob', 12)
```
new 方式创建对象过程：
(1) 创建一个新的对象。
(2) 将构造函数的作用域赋给新对象（因此`this`就指向了这个新对象）。
(3) 执行构造函数中的代码（为这个新对象添加属性）。
(4) 返回新对象。

<!-- more -->

```js
// 实例的 constructor(构造函数) 属性指向 Person
p1.constructor === Person // true
```
```js
p1 instanceof Object // true
p1 instanceof Person // true
```

方法如果定义在构造函数上，那么每个实例都会有各自的方法，这其实是没有必要的，所以，方法一般都定义到原型上（共享）。


原型对象
每个函数都会有一个 prototype 属性，这个属性指向函数的原型对象。所有原型对象都会自动获得一个 constructor 属性，这个属性包含一个指向 prototype 属性所在函数的指针。
```js
Person.prototype.isPrototypeOf(p1) // true
```
实例的`__proto__`属性指向原型对象
```js
// 返回实例的 原型
Object.getPrototypeOf(p1) === Person.prototype
```

> 实例 能直接访问到构造函数，其实是因为原型上存在初始的属性 constructor 指向构造函数

hasOwnProperty() 方法可以检测一个属性是存在于实例还是原型（这个方法是从 Object 继承而来）
```js
function Person(name) {
  this.name = name
}
Person.prototpye.age = 10
var p1 = new Person()
p1.hasOwnProperty('name') // true 因为构造函数中是有name属性的
p1.hasOwnProperty('age') // false
p1.hasOwnProperty('bb') // false
```

原型与 in 操作符

in 如果 实例 或者 原型中存在 就返回 true

```js
// 判断 是不是原型中的属性
function hasPrototypeProperty(object, name) {
  return !object.hasOwnProperty(name) && (name in object)
}
```

for - in 返回的是所有能够通过对象访问的，可枚举的属性，包括实例中的属性和原型中的属性。

Object.keys() 获取实例上所有可枚举的属性，只是实例哦，不包括原型上的属性。
Object.getOwnPropertyNames() 获取实例上所有属性（包括可枚举和不可枚举的），只是实例，不包括原型上的属性。

如下，我们将 Person.prototype 设置为等于一个以对象字面量形式创建的新对象，这样做唯一的影响是 constrcutor 属性不再指向 Person 了。

> 每创建一个函数，就会同时创建它的 prototype 对象，这个对象也会自动获得 constructor 属性。而我们在这里直接重写了默认的 prototype 对象，因此 constructor 属性也就变成了新对象的 constructor 属性（指向 Object 的构造函数），不再指向 Person 函数了。

```js
function Person() {
}
Person.prototype = {
}
var p = new Person()
p instanceof Object // true
p instanceof Person // true
p.constructor === Person // false
p.constructor === Object // true
```
可以 强制 指定 `Person.prototype.constructor = Person` 这样的话 constructor 属性就是可枚举了
```js
Object.defineProperty(Person.prototype, "constructor", {
  enumerable: false,
  value: Person
})
```

我们可以随时为原型添加属性和方法，并且修改能够立即在所有对象实例中反映出来，但是，如果重写整个原型对象，那么情况就不一样了
```js
function Person() {

}
var p = new Person()

Person.prototype = {
  constructor: Person,
  name: 23,
  sayName: function() {
    alert(this.name)
  }
}

p.sayName() // error
```
简单说 实例上的 [[Prototype]] 属性还是指向 最初创建实例时 的原型对象。


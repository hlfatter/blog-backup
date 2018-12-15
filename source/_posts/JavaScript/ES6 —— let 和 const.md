---
title: ES6 —— let 和 const
author: Deepspace
top: true
img: https://github.com/IDeepspace/ImageHosting/raw/master/bg/1.jpg 
tags:
  - ES6
categories: JavaScript
date: 2017-01-28
urlname: es6-let-const
---

<!-- ## ES6 —— let 和 const -->

#### 一、let 声明变量

在 ES5 中，除了全局作用域外，限定所声明的变量的作用域是函数作用域。这使得 ES5 在很多情况下为了模拟块级作用域（避免变量名污染）需要使用立即执行的匿名函数。在 ES6 中新增了声明块级使用域变量的关键字 let。与 var 相比，使用 let 声明的变量有如下特点：

1. let 声明的变量所在的作用域为块级;
2. let 声明的变量不存在变量提升;
3. let 声明的变量不允许重复声明，否则会报错。 使用 let 可以替代 ES5 中为了模拟块级作用域而使用的立即执行的匿名函数:

<!-- more -->

```javascript
//ES5实现方法:
(function() {
  for (var i = 0; i < 10; i++) {
    //...
  }
})();
//ES6实现方法:
for (let i = 0; i < 6; i++) {
  //...
}
```

我们具体来看看它的特点

**1 . 所声明的变量，只在 let 命令所在的代码块内有效**

```javascript
{
  let a = 10;
  var b = 1;
}

a; // ReferenceError: a is not defined.
b; // 1
```

有了这个特性,我们经常使用 for 循环的计数器，就很合适使用 let 命令。

```javascript
for (let i = 0; i < arr.length; i++) {}
console.log(i);
//ReferenceError: i is not defined
```

**2 . let 不像 var 那样，会发生“变量提升”现象。**

```javascript
function do_something() {
  console.log(foo); // ReferenceError
  let foo = 2;
}
```

上面代码在声明 foo 之前，就使用这个变量，结果会抛出一个错误。

**3 . 不允许重复声明**

```javascript
// 报错
{
  let a = 10;
  var a = 1;
}
// 报错
{
  let a = 10;
  let a = 1;
}
```

因此，不能在函数内部重新声明参数。

```javascript
function func(arg) {
  let arg; // 报错
}
function func(arg) {
  {
    let arg; // 不报错
  }
}
```

**4 . 另外，ES6 也规定，函数本身的作用域，在其所在的块级作用域之内。如 :**

```javascript
function f() {
  console.log('I am outside!');
}
(function() {
  if (false) {
    // 重复声明一次函数f
    function f() {
      console.log('I am inside!');
    }
  }
  f();
})();
```

上面代码在 ES5 中运行，会得到“I am inside!”，但是在 ES6 中运行，会得到“I am outside!”。这是因为 ES5 存在函数提升，不管会不会进入 if 代码块，函数声明都会提升到当前作用域的顶部，得到执行；而 ES6 支持块级作用域，不管会不会进入 if 代码块，其内部声明的函数皆不会影响到作用域的外部。

需要注意的是，如果在严格模式下，函数只能在顶层作用域和函数内声明，其他情况（比如 if 代码块、循环代码块）的声明都会报错。

#### 二、const 声明常量

ES6 中可以使用 const 关键字来声明常量，被声明的常量是不能被修改的。与使用 let 声明的变量类似，const 声明的常量为块级作用域，不存在变量提升，且不可重复声明。

```javascript
const PI = 3.1415;
PI; // 3.1415
PI = 3;
PI; // 3.1415,这里赋值无效,但是不会报错
const PI = 3.1;
PI; // 3.1415,这里重复声明了
```

上面代码表明改变常量的值是不起作用的。需要注意的是，对常量重新赋值不会报错，只会默默地失败。

**注意 :**

const 实际上保证的，并不是变量的值不得改动，而是变量指向的那个内存地址不得改动。对于简单类型的数据（数值、字符串、布尔值），值就保存在变量指向的那个内存地址，因此等同于常量。但对于复合类型的数据（主要是对象和数组），变量指向的内存地址，保存的只是一个指针，const 只能保证这个指针是固定的，至于它指向的数据结构是不是可变的，就完全不能控制了。因此，将一个对象声明为常量必须非常小心。 看个例子 :

```javascript
const foo = {};
// 为 foo 添加一个属性，可以成功
foo.prop = 123;
foo.prop; // 123
// 将 foo 指向另一个对象，就会报错
foo = {}; // TypeError: "foo" is read-only
```

另一个例子:

```javascript
const a = [];
a.push('Hello'); // 可执行
a.length = 0; // 可执行
a = ['Dave']; // 报错
```

如果要使对象为常量的话可以配合 Object.freeze( )函数来实现 :

```javascript
const foo = Object.freeze({ a: 1 });

foo = { b: 2 }; //无法修改foo,严格模式下报错

foo.a = 2; //无法修改foo.a

console.log(foo.a); //1
```

以上方法中的 `Object.freeze()` 函数本身有局限性，它只能冻结对象的属性不被修改，并不能冻结它的属性的属性被修改。如果要实现将对象内部所有属性冻结，需要使用自定义的强化的冻结函数。下面是一个深度冻结的方法:

```javascript
Object.deepFreeze = function(object) {
  Object.freeze(object);
  Object.keys(object).forEach(function(key) {
    if (typeof object[key] == 'object') return Object.deepFreeze(object[key]);
  });
};
```

通过以上 deepFreeze 即可实现完全将对象常量化。效果如下：

```javascript
const foo = Object.freeze({ a: [1] }); //使用原始的冻结函数

let a = foo.a.push(2); //本操作可以使foo.a变为[1,2]

console.log(foo.a); //[1,2]

const foo2 = Object.deepFreeze({ a: [1] }); //使用深度冻结函数

let b = foo2.a.push(2); //本操作无法改变foo2.a ,会报错

//console.log(b);
```

#### 三、全局对象属性

全局对象是最顶层的对象，在浏览器环境指的是 window 对象，在 Node.js 指的是 global 对象。ES5 规定，所有全局变量都是全局对象的属性。

ES6 规定，var 命令和 function 命令声明的全局变量，属于全局对象的属性；let 命令、const 命令、class 命令声明的全局变量，不属于全局对象的属性。例 :

```javascript
var a = 1;
// 如果在Node的REPL环境，可以写成global.a
// 或者采用通用方法，写成this.a

window.a; // 1

let b = 1;
window.b; // undefined
```

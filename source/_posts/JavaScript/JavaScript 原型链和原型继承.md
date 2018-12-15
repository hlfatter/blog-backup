---
title: JavaScript 原型链和原型继承
author: Deepspace
top: true
img: https://github.com/IDeepspace/ImageHosting/raw/master/bg/5.jpg 
tags:
  - ES6
categories: JavaScript
date: 2017-01-28
urlname: es6-proptype-chain
---

<!-- ## JavaScript 原型链和原型继承 -->

JavaScript 中最难最繁复的一个知识点应该就是原型继承, 要说清楚原型继承，就得先了解清楚什么是原型链。

### 一.原型链

在写 JavaScript 的时候，我们经常会写出下面的代码：

```
const arr = [1, 2, 3];
arr.push(4); // 4

const string = 'Deepspace';
string.slice(1);//eepspace
```

那么我们会不会觉得很奇怪, 为什么我好端端的创建一个数组或者字符串，莫名其妙地就有了 `.push`, `.slice` 这些方法呢？

<!-- more -->

实际上, 我们用字面量创建字符串和数组是对 `Array` 和 `String` 进行了实例化 .

看看 MDN 上面是怎么说的 :

> 当谈到继承时，Javascript 只有一种结构：对象。每个对象都有一个私有属性(称为[[Prototype]])，它持有一个连接到另一个称为其 prototype 对象的链接。该原型对象具有一个自己的原型，等等，直到达到一个对象的 prototype 为 null。根据定义，null 没有 prototype，并作为这个原型链 中的最后一个环节。
>
> JavaScript 中几乎所有的对象都是位于原型链顶端的 Object 的实例。
>
> ......
>
> JavaScript 对象是动态的属性“包”（指其自己的属性）。JavaScript 对象有一个指向一个原型对象的链。当试图访问一个对象的属性时，它不仅仅在该对象上搜寻，还会搜寻该对象的原型，以及该对象的原型的原型，依次层层向上搜索，直到找到一个名字匹配的属性或到达原型链的末尾。

我们来验证一下 MDN 的说法 :

```
arr.hasOwnProperty('push');     // false
string.hasOwnProperty('slice'); // false
```

`hasOwnProperty` 是用来检测指定的属性是否在对象本身上的，上面的两行代码都返回了 `false`。那么显然， `.push`, `.slice` 是原型链上的，那么 JavaScript 引擎是如何找到这两个方法的呢？

实际上，根据 ECMAScript 标准，每个对象都有一个 `[[prototype]]` 属性，用来指向该对象的原型，这等同于某些浏览器内核给 JavaScript 实现的 `__proto__` 属性（已在 ES6 中成为标准）。从 ECMAScript 6 开始，`[[Prototype]]`还可以用 `Object.getPrototypeOf()`和 `Object.setPrototypeOf()` 访问器来访问。

我们用下面的例子来说明 :

```
const o = {
    a: 1,
    b: 2
};
// 演示使用，尽量不要直接操作 .__proto__
// 否则可能造成原型链混乱
o.__proto__ = {
    b: 3,
    c: 4
};
// 上面两行代码执行完，我们就创建了这么一段原型链：
// { a:1, b:2 } --> { b:3, c:4 } --> Object.prototype --> null

console.log(o.a); //1
// a 是 o 的自身属性吗？是的，该属性的值为 1

console.log(o.b); // 2
// b 是 o 的自身属性吗？是的，该属性的值为 2
// o.[[Prototype]] 上还有一个 b 属性，但是它不会被访问到。
// 这种情况称为“属性遮蔽 (property shadowing)”。

console.log(o.c); // 4
// c 是 o 的自身属性吗？不是，那看看 o.[[Prototype]] 上有没有。
// c 是 o.[[Prototype]] 的自身属性吗？是的，该属性的值为 4

console.log(o.d); // undefined
// d 是 o 的自身属性吗？不是，那看看 o.[[Prototype]] 上有没有.
// d 是 o.[[Prototype]] 的自身属性吗？不是，那看看 o.[[Prototype]].[[Prototype]] 上有没有。
// o.[[Prototype]].[[Prototype]] 为 null，停止搜索。
// 没有 d 属性，返回 undefined。
```

那么同样的, 文章刚开始的两段代码也是创建了原型链 :

```
// [1, 2, 3] --> Array.prototype -> Object.prototype --> null
const arr = [1, 2, 3];
console.log(arr.__proto__ === Array.prototype); // true
arr.push(4); // 4
// Array.prototype.push

// [1, 2, 3] --> String.prototype -> Object.prototype --> null
const str = 'Deepspace';
console.log(str.__proto__ === String.prototype); // true
str.slice(1); // eepspace
// String.prototype.slice
```

**那么对于函数又是怎么一回事呢？**

JavaScript 全部都是动态的，都是运行时，不存在类（classes）。实际上，在 JavaScript 中，所谓的类的构造器，其实就是一个普通的函数。当使用 `new` 来作用这个函数时，它就可以被称为构造方法（构造函数），比如：

```
const Person = function (name, age) {
    this.name = name;
    this.age = age
};
Person.prototype.say = function () {
    return 'hello!'
};
// 创建了一个原型链
// Deepspace --> Person.prototype --> Object.prototype --> null
const Deepspace = new Person('Deepspace', 22);
console.log(Deepspace.say()); // hello!
console.log(Deepspace.__proto__ === Person.prototype); // true
```

可见，`Deepspace.__proto__` 指向了构造函数 `Person` 的 `prototype` , 形成了一个原型链。

### 二.原型继承

通过上面的描述, 我们已经对原型链有所了解. 我们再来看看原型继承 .

继承是面向对象的特性之一，JavaScript 当然也可以，只不过不是类继承，而是原型继承：

```
const Animal = function () {};
Animal.prototype.run = function () {
    console.log('Animal run fast!')
};

// 继承 Animal
const Person = function (name, age) {
    this.name = name;
    this.age = age;
};
Person.prototype = new Animal();

Person.prototype.constructor = Person;

Person.prototype.say = function () {
    console.log('hello!')
};

// Deepspace --> Person.prototype --> Animal.prototype --> Object.prototype --> null
const Deepspace = new Person('Deepspace', 22);
Deepspace.run(); // Animal run fast!
Deepspace.say(); // hello!
console.log(Deepspace.name);  // Deepspace
console.log(Deepspace.age);  // 22
```

我们首先创造了一个名为 `Animal` 的构造函数，并给他的原型加了一个 `run()` 的方法。

接着我们创建了一个名为 `Person` 的构造函数，然后，我们将 `Person` 的原型指向了 `Animal` 的实例：

```
Person.prototype = new Animal()
```

这个的用处是，我们想把 `Animal` 原型上定义的方法继承下来，你也可以采用下面的办法：

```
Person.prototype = Animal.prototype
```

但是**不建议**这么写，**因为这样会让 `Person` 和 `Animal` 共用了一个原型对象**，那么之后我们对 `Person.prototype` 的修改也会相应的修改到 `Animal.prototype`，这是我们不希望的。

然后我们把 `Person.prototype.constructor` 指回了 `Person` 构造函数：

```
Person.prototype.constructor = Person
```

这是因为我们执行 `Person.prototype = new Animal()` 的时候，`Person.prototype.constructor` 会被改成 `Animal`，那么之后的 `Deepspace` 的构造函数就变成 `Animal` ，这是不对的，于是我们做了修正。

然后我们就可以愉快的给 `Person` 添加其他的方法了，比如我加了一个 `say`。那么实例出 `Deepspace` 我们可以看到，`Deepspace` 同时继承了 `Animal` 的 `run` 和 `Person` 的 `say` 方法，实现了原型继承。

### 三.ES6 的语法糖

可以看到，使用原生来写原型继承实在是太麻烦了，于是 ES6 添加了一个语法糖—— `class`，新的 `class`写法只是让对象原型的写法更加清晰、更像“面向对象”编程的语法而已。

我们把上面的例子改写成 ES6 `class` 的形式：

```
// Animal 类
class Animal {
    // 定义构造函数
    constructor () {}
    // 定义 run 方法
    run () {
        console.log('Animal run fast!')
    }
}

// Person 类 继承于 Animal
class Person extends Animal {
    // 重写 Animal 的构造函数
    constructor (name, age) {
        super();
        this.name = name;
        this.age = age
    }
    // 添加 say 方法
    say () {
        console.log('hello!')
    }
}

const Deepspace = new Person('Deepspace', 22);
Deepspace.run(); // Animal run fast!
Deepspace.say(); // hello!
console.log(Deepspace.name);  // Deepspace
console.log(Deepspace.age);  // 22
```

有一点要注意，在继承的时候，构造函数内要记得使用 `super` 来调用父类的构造函数，否则新建实例时会报错。这是因为子类没有自己的 `this` 对象，而是继承父类的 `this` 对象，然后对其进行加工。如果不调用 `super` 方法，子类就得不到 `this` 对象。

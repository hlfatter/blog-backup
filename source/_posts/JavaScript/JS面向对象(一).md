---
title: JS 面向对象(一)
author: Deepspace
top: true
img: https://github.com/IDeepspace/ImageHosting/raw/master/bg/6.jpg 
tags:
  - JavaScript
categories: JavaScript
date: 2016-10-28
urlname: js-oo-1
---

<!-- ## JS 面向对象(一) -->

### 一、什么是面向对象编程？

维基百科上是这样说的

> Object-oriented programming (OOP) is a programming paradigm based on the concept of "objects", which may contain data, in the form of fields, often known as attributes; and code, in the form of procedures, often known as methods.

翻译一下：面向对象编程是一套基于“对象”这个概念的编程方法论。一个对象中会包含数据，通常叫“属性”，也会包含一些函数，通常叫方法。

#### 1.class 和 object

object 就是对象的英文，class 中文翻译为”类”，这两个概念是面向对象编程的最重要的概念。对象是类的实例化，例如人是一个类，人有名字这个属性，但是没有具体值。 把人这个类实例化成一个具体的人，给这个人具体赋值，这个具体的人，就是一个对象，对象中就有了实际的数据值了。

<!-- more -->

#### 2.为何要面向对象编程？

面向过程其实最为实际的一种思考方式，因为我们总是一贯一步一步的解决问题。可以说面向过程是一种基础的方法，它考虑的是实际的实现。 所以面向过程编程其实是更对新手直观的，但是面向对象编程因为对数据和方法进行了封装，造成类是有极强的可复用性的，所以在大型项目中面向对象 几乎是标配了。

还是有一个有意思的小点要提一下，面向对象编程跟最直观的面向过程的编程有很大的区别。当然任何的编程都要考虑两个要素，一个是数据，另外一个是方法。面向对象先考虑 的是数据，后考虑方法，而面向过程编程是相反的。

### 二、使用 Babel 编译 ES6

并不是所有的浏览器都支持 ES6（ES2015）的各种新语法。如果我们要把用 ES6 写的代码运行到产品环境下，还是要把 ES6 语法编译成 ES5 （也就是老语法）以获得 最佳的浏览器支持的。目前，工程实践中最常用的做法是用 Babel 来完成这个编译工作。具体怎么做呢？

#### 1.初始化一个项目

我们新建一个文件夹，就叫作 `oo-js-demo` 吧，执行：

```
$ npm init -y
```

#### 2.新建一个 html 文件

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>o-o-js</title>
</head>
<body>
  <script src="build/main.js"></script>
</body>
</html>
```

我们要在 `src` 文件夹中新建 `main.js` , 简单写入 ES6 的一些新语法，然后使用 Babel 将其编译成 ES5 的语法，编译后的文件放在 `build` 文件夹下的 `main.js` , 这样在 html 中引入的 js 文件就可以在不支持 ES6 语法的浏览器中运行了。

#### 3.src/main.js

```
class Person {
  constructor(name) {
    this.name = name
  }
}

var Deepspace = new Person('Deepspace')
console.log(Deepspace.name)
```

#### 4.使用 Babel

安装

```
$ npm install --save-dev babel-cli
$ npm install babel-preset-env --save-dev
```

在根目录下创建一个 `.babelrc` 文件，写入以下内容：

```
{
  "presets": ["env"]
}
```

然后在 `package.json` 中将 Babel 的命令写入进去：

```
{
  "name": "oo-js-demo",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "build": "babel src -d build"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "babel-cli": "^6.26.0",
    "babel-preset-env": "^1.6.1"
  }
}
```

最后，执行：

```
$ npm run build
```

这样，就会在根目录创建 `build` 文件夹，并将编译好的 ES5 语法写入 `build/main.js` 中。

### 三、Class 核心知识

#### 1.写一个 Class

我们先用 ES5 的语法写个类

```
function Person(name) {
  this.name = name;
}

Person.prototype.changeName = function(newName) {
  this.name = newName;
};
```

ES6 语法：

```
class Person {
  constructor(name) {
    this.name = name;
  }
  changeName(newName) {
    this.name = newName;
  }
}
```

使用的时候都是一样的，都用 new 关键字

```
var Deepspace = new Person('Deepspace');
console.log(Deepspace.name);

Deepspace.changeName('Deepspace');
console.log(Deepspace.name);
```

#### 2.理解 this 的指向

> 在面向对象编程条件下，类的方法内部如果含有 this，它默认指向类的实例。

```
class Person {
  constructor(name) {
    this.name = name;
    console.log(this);
  }
  changeName(newName) {
    this.name = newName;
  }
  sayName(){
    console.log(this.name);
  }
}

let deepspace = new Person('deepspace');
let togoblog = new Person('togoblog');
deepspace.changeName('cxin');
deepspace.sayName();
```

打印出来的结果是：

```
Person { name: 'deepspace' }
Person { name: 'togoblog' }
cxin
```

我们也可以使用“对象字面量”的形式来定义对象 :

```
let Person = {
  name: 'Peter',
  talk: function() {
    console.log(`My name is ${this.name}`);
  }
}

Person.talk();
```

#### 3.bind

this 在不同的执行上下文中指向不同的对象，这样往往会造成 undefined 错误，bind 要解决的就是明确函数在执行的时候 this 的指向。

理解 bind 和 this 是使用 JS 对象的必备基础。我们看下面的例子：

```
let Person = {
  name: 'Deepspace',
  talk: function() {
    console.log(`My name is ${this.name}`);
  }
};

Person.talk();
let plzTalk = Person.talk;
plzTalk();
```

执行的结果会是这样的：

```
My name is Deepspace
My name is undefined
```

为什么呢？其实 `let plzTalk = Person.talk;` 可以写成这样：

```
let plzTalk = function() {
  console.log(`My name is ${this.name}`);
}
```

那 `this.name` 自然就是 undefined 的了。

**使用 bind 之后** :

```
let Person = {
  name: 'Deepspace',
  talk: function() {
    console.log(`My name is ${this.name}`);
  }
};

Person.talk();
let plzTalk = Person.talk;
plzTalk.bind(Person)();
```

也可以写成下面这样：

```
let Person = {
  name: 'Deepspace'
}

let plzTalk = function() {
    console.log(`My name is ${this.name}`);
};

plzTalk.bind(Person)();
```

所以总结一下： bind 的作用就是把一个对象（作为 bind 的参数传入），绑定到这个函数中的 this 之上。

#### 4.bind 的例子

再举一个 bind/this 的小例子，加深一下理解。

看下面这个例子：

```
function talk() {
  console.log(this.sound);
}

let Person = {
  sound: 'Hi there!',
  speak: talk
};

Person.speak();
```

执行后的结果：

```
Hi there!
```

是可以正确输出 sound 的值的。其实是把 talk 这个函数拷贝到了 Person 对象中的 speak 中的 talk，这时候 this 就是指向 Person 这个对象，所以可以正确输出！

所以，这样做也是可以的：

```
function talk() {
  console.log(this.sound);
}

let Person = {
  sound: 'Hi there!',
};

talk.bind(Person)();
```

#### 5.静态方法

我们在调用 class 里面的方法的时候，需要先将类实例化成对象，然后调用对象中的方法，这是很不方便的。

静态方法（ static method ）可以让我们不用实例化 class ，直接呼叫，很适合用来组织很多小功能到一个 Class 之上。

我们先看下面的错误示范：

```
class Person {
  constructor() {

  }
  sayHello() {
    console.log("Hello");
  }
}

Person.sayHello();
```

会报错：

```
TypeError: Person.sayHello is not a function
```

我们只需要在方法前加上 `static` 关键字即可

```
class Person {
  constructor() {

  }
  static sayHello() {
    console.log("Hello");
  }
}

Person.sayHello();
```

运行一下，会正确打印出 `Hello` , OK !

### 四、继承

#### 1.父类和子类

面向对象编程中所谓”继承”，指的就是子类会继承父类的属性和方法。ES6 语法中，继承通过 extends 关键字来实现。

**通过下面的代码来演示这几个效果：**

- 子类中可以使用父类中的属性
- 子类中可以调用父类中的方法
- 子类中可以扩展自己的属性和方法

```
class Father {
  constructor() {
    this.gender = 'male';
  }
  getFamilyName() {
    console.log(`The family name is Zhang`);
  }
}

class Son extends Father {
  constructor() {
    super();
    this.height = 160;
  }
  getSchoolName() {
    console.log('NO.2 Middle School');
  }
}

let tom = new Son;
console.log(tom.gender);
console.log(tom.height);
tom.getFamilyName();
tom.getSchoolName();
```

#### 2.super 调用父类方法

使用类继承的过程中，理解 super() 的作用是非常必要的。

**super 是什么？**

可以这样简单的认为： super 代表父类。主要有两个用途：

- 使用 super() 呼叫父类的 constructor()
- 使用 `super.functionName()` 调用父类中的 static 方法

**super() 的作用**

子类必须在 constructor 方法中调用 super 方法，否则新建实例时会报错。这是因为子类没有自己的 this 对象，而是继承父类的 this 对象，然后对其进行加工。如果不调用 super 方法，子类就得不到 this 对象。

ES6 的继承机制，实质是先创造父类的实例对象 this（所以必须先调用 super 方法），然后再用子类的构造函数修改 this。

```
class Father {
  constructor(familyName){
    this.familyName = familyName;
  }

  getFamilyName(){
    return `The family name is ${this.familyName}`;
  }
  static sayHello() {
    console.log('hello');
  }
}

class Son extends Father {
  constructor(familyName, height) {
    super(familyName);
    this.height = height; // 没有上一行的 super() ，这里的 this 就不让用
  }

  getSchool(){
    return 'NO.2 Middle School!';
  }
  static hello() {
    super.sayHello(); // 调用父类的静态方法
  }
}

let tom = new Son('Chen', 180);
console.log(tom.height);
console.log(tom.getFamilyName());
console.log(Son.hello());
```

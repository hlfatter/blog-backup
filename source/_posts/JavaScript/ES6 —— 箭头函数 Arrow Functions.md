---
title: ES6 —— 箭头函数 Arrow Functions
author: Deepspace
top: true
img: https://github.com/IDeepspace/ImageHosting/raw/master/bg/0.jpg 
tags:
  - ES6
categories: JavaScript
date: 2017-02-18
urlname: es6-arrow-functions
---

<!-- ## ES6 —— 箭头函数 Arrow Functions -->

ES6 中引入了一种编写函数的新语法 ---- 箭头函数 Arrow Functions

#### 一. 箭头函数产生的目的

- 简洁语法
- 与父作用域共享关键字`this`

#### 二. 箭头函数的优点

- 使用箭头函数比普通函数少些动词，如：`function`或 `return`
- `this`提前定义，从上下文可以捕获`this`。

当你只需要一个只有一个参数的简单函数时，可以使用新标准中的箭头函数，它的语法非常简单：`标识符=>表达式`。你无需输入 `function`和 `return`，一些小括号、大括号以及分号也可以省略。

<!-- more -->

#### 三. 箭头函数的用法

##### 1.如果箭头函数不需要参数或需要多个参数，就使用一个圆括号代表参数部分。

```javascript
let f = () => 5;
// 等同于
let f = function() {
  return 5;
};

let sum = (num1, num2) => num1 + num2;
// 等同于
let sum = function(num1, num2) {
  return num1 + num2;
};
```

##### 2.如果箭头函数的代码块部分多于一条语句，就要使用大括号将它们括起来，并且使用 return 语句返回

```javascript
let sum = (num1, num2) => {
  return num1 + num2;
};

//如果箭头函数只有一行语句，且不需要返回值，可以采用下面的写法，就不用写大括号了。
let fn = () => void doesNotReturn();
```

##### 3.由于大括号被解释为代码块，所以如果箭头函数直接返回一个对象，必须在对象外面加上括号，否则会报错。

```javascript
// 报错
let getTempItem = id => { id: id, name: "Temp" };

// 不报错
let getTempItem = id => ({ id: id, name: "Temp" });
```

##### 4.箭头函数还可以和解构赋值 `Destructuring` 联合使用.

```javascript
const full = ({ first, last }) => first + ' ' + last;

// 等同于
function full(person) {
  return person.first + ' ' + person.last;
}
```

##### **5.箭头函数的一个用处是简化回调函数**

```javascript
// 正常函数写法
[1, 2, 3].map(function(x) {
  return x * x;
});

// 箭头函数写法
[1, 2, 3].map(x => x * x);
```

##### 6.rest 参数与箭头函数结合

```javascript
const numbers = (...nums) => nums;

numbers(1, 2, 3, 4, 5);
// [1,2,3,4,5]

const headAndTail = (head, ...tail) => [head, tail];

headAndTail(1, 2, 3, 4, 5);
// [1,[2,3,4,5]]
```

#### 四.箭头函数的特性

##### 1、typeof 运算符和普通的函数一样

```javascript
let commFunc = () => {};
console.log(typeof commFunc);
```

输出为`function`。

```javascript
let arrowFunc = () => {};
console.log(typeof arrowFunc);
```

输出也为`function`。
从此可以看出箭头函数的类型和普通的函数一样都为`function`。

##### 2、`instanceof`也返回`true`，表明是 Function 的实例

```javascript
let func = () => {};
console.log(func instanceof Function);
```

输出为`true`，由此可以看出箭头函数也是 Function 的实例

##### 3、`this`的指向

普通函数与箭头函数有个微小的不同点。 **箭头函数没有自己的 this 值** ，其 this 值是通过继承其它传入对象而获得的, 通常来说是上一级外部函数的 `this` 的指向 。

```javascript
function f() {
  setTimeout(() => {
    console.log('id:', this.id);
  }, 100);
}

var id = 21;

foo.call({ id: 42 }); // id: 42
```

这个例子中， `setTimeout` 的参数是一个箭头函数， 每隔 100 毫秒运行一次，如果是普通函数，执行的 `this` 应该指向全局对象， 但是箭头函数会让 `this` 总是指向函数所在的对象

**箭头函数里面嵌套箭头函数会有多少个 this 呢？**
看一个简单的例子

```javascript
function f() {
  return () => {
    return () => {
      return () => {
        console.log('id:', this.id);
      };
    };
  };
}
f().call({ id: 42 })()()(); //id: 42
```

上面的代码中只有一个 `this`， 就是函数 f 的`this` 。这是因为所有的内层函数都是箭头函数，都没有自己的 `this`，都是最外层 `f ` 函数的 `this`。

**注意:** 还有三个变量在箭头函数中也是不存在的 `arguments` , `super`, `new.target`。所以，箭头函数也就不能再用这些方法`call()`,`apply()`,`bind()`,因为这是一些改变 this 指向的方法，
箭头函数并没有 `this`。

```javascript
let adder = {
  base: 1,
  add: function(a) {
    var f = v => v + this.base;
    return f(a);
  },
  addThruCall: function(a) {
    var f = v => v + this.base;
    var b = { base: 2 };
    return f.call(b, a);
  }
};
console.log(adder.add(1));
```

##### 4、返回对象用小括号括起来

```javascript
let person = () => {
  name: 'galler';
};
console.log(person());
```

输出为`undefined`。此时的"{}"表明函数的起始位置和结束位置，由于该函数没有返回值，所以被调用时值为 undefined

```javascript
let person = () => ({
  name: 'galler'
});
console.log(person());
```

输出为`{name:"galler"}`。 此时"{}"表示定义一个对象。用"()"括起来表示是一个表达式，默认返回该对象。

##### 5、箭头函数中不能使用`new`

```javascript
let Person = name => {
  this.name = name;
};
let one = new Person('galler');
```

运行该程序，则出现`TypeError: Person is not a constructor`

##### 6、arguments

```javascript
function person() {
  console.log(arguments);
}
person(1);
```

一般的函数使用`arguments`，在浏览器中输出为一个数组：`[1]`，在 Node 环境中输出为一个对象：`{'0':1}`

```javascript
let person = () => {
  console.log(arguments);
};
person('galler');
```

箭头函数使用`arguments`，在浏览器环境中，则出现`ReferenceError`，在 Node 环境中输出`{"0":{}，……}`。
由此可以得出，箭头函数与普通函数的再一个区别：不能使用`arguments`对象。

##### 7、没有原型

```javascript
let person = () => {};
console.log(person.prototype);
```

输出为`undefined`。由此可以看出箭头函数没有原型 。

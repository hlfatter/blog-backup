---
title: ES6 —— 模板字符串
author: Deepspace
top: true
img: https://github.com/IDeepspace/ImageHosting/raw/master/bg/5.jpg
tags:
  - ES6
categories: JavaScript
date: 2017-02-01
urlname: es6-string
---

<!-- ## ES6 —— 模板字符串 -->

我们知道, 在传统的 JavaScript 语言，输出模板通常是这样写的 , 需要用`+`来拼接 :

```javascript
$('#result').append(
  'There are <b>' +
    basket.count +
    '</b> ' +
    'items in your basket, ' +
    '<em>' +
    basket.onSale +
    '</em> are on sale!'
);
```

上面这种写法相当繁琐不方便.

<!-- more -->

#### ES6 引入了一种新型的字符串字面量语法，我们称之为模板字符串（template strings）。

##### 1.模板字符串（template string）是增强版的字符串，用反引号（`）标识。

##### 2.它可以当作普通字符串使用，也可以用来定义多行字符串，或者在字符串中嵌入变量。

##### 3.模板字符串中嵌入变量，需要将变量名写在 `${}` 之中 。

##### 4.如果在模板字符串中需要使用反引号，则前面要用反斜杠转义。

##### 5.如果使用模板字符串表示多行字符串，所有的空格和缩进都会被保留在输出之中。

##### 6.所有模板字符串的空格和换行，都是被保留的，比如`<ul>`标签前面会有一个换行。如果你不想要这个换行，可以使用`trim`方法消除它

**看看下面的例子 :**

```javascript
//1. 普通字符串
`In JavaScript '\n' is a line-feed.`;

// 2 .字符串中嵌入变量
let name = 'Bob',
  time = 'today';
console.log(`Hello ${name}, how are you ${time}?`);

// 2.1如果变量没有声明,则会报错 !变量place没有声明
let msg = `Hello, ${place}`;
// 报错

//3 .反斜杠转义
let greeting = `\`Yo\` World!`;
console.log(greeting);

// 4.多行字符串
console.log(`string text line 1
string text line 2`);

//5.使用模板字符串表示多行字符串，所有的空格和缩进都会被保留在输出之中
$('#list').html(`
<ul>
  <li>first</li>
  <li>second</li>
</ul>
`);

//6. 可以使用trim方法消除换行
$('#list').html(
  `
<ul>
  <li>first</li>
  <li>second</li>
</ul>
`.trim()
);
```

##### 7.大括号内部可以放入任意的 JavaScript 表达式，可以进行运算，以及引用对象属性。

```javascript
let x = 1;
let y = 2;

`${x} + ${y} = ${x + y}``${
  x // "1 + 2 = 3"
} + ${y * 2} = ${x + y * 2}`;
// "1 + 4 = 5"

let obj = { x: 1, y: 2 };
`${obj.x + obj.y}`;
// "3"
```

##### 8.模板字符串之中还能调用函数。

```javascript
function fn() {
  return 'Hello World';
}

`foo ${fn()} bar`;
// foo Hello World bar
```

如果大括号中的值不是字符串，将按照一般的规则转为字符串。比如，大括号中是一个对象，将默认调用对象的`toString`方法。

##### 9.由于模板字符串的大括号内部，就是执行 JavaScript 代码，因此如果大括号内部是一个字符串，将会原样输出。

```javascript
`Hello ${'World'}`;
// "Hello World"
```

##### 10.模板字符串甚至还能嵌套。

```javascript
const tmpl = addrs => `
  <table>
  ${addrs
    .map(
      addr => `
    <tr><td>${addr.first}</td></tr>
    <tr><td>${addr.last}</td></tr>
  `
    )
    .join('')}
  </table>
`;
```

上面代码中，模板字符串的变量之中，又嵌入了另一个模板字符串，使用方法如下。

```javascript
const data = [
  { first: '<Jane>', last: 'Bond' },
  { first: 'Lars', last: '<Croft>' }
];

console.log(tmpl(data));
// <table>
//
//   <tr><td><Jane></td></tr>
//   <tr><td>Bond</td></tr>
//
//   <tr><td>Lars</td></tr>
//   <tr><td><Croft></td></tr>
//
// </table>
```

##### 11.如果需要引用模板字符串本身，在需要时执行，可以像下面这样写。

```javascript
// 写法一
let str = 'return ' + '`Hello ${name}!`';
let func = new Function('name', str);
func('Jack'); // "Hello Jack!"

// 写法二
let str = '(name) => `Hello ${name}!`';
let func = eval.call(null, str);
func('Jack'); // "Hello Jack!"
```

---
title: ES6 —— 数组的拓展
author: Deepspace
top: true
img: https://github.com/IDeepspace/ImageHosting/raw/master/bg/3.jpg 
tags:
  - ES6
categories: JavaScript
date: 2017-02-11
urlname: es6-array
---

<!-- ## ES6 —— 数组的拓展 -->

#### 一 . Array.from()

`Array.from`方法用于将两类对象转为真正的数组：类似数组的对象（array-like object）和可遍历（iterable）的对象（包括 ES6 新增的数据结构 Set 和 Map）

下面是一个类似数组的对象，`Array.from`将它转为真正的数组。

```javascript
let arrayLike = {
  '0': 'a',
  '1': 'b',
  '2': 'c',
  length: 3
};

// ES5的写法
var arr1 = [].slice.call(arrayLike); // ['a', 'b', 'c']

// ES6的写法
let arr2 = Array.from(arrayLike); // ['a', 'b', 'c']
```

<!-- more -->

#### 二 . Array.of()

`Array.of`方法用于将一组值，转换为数组。

```javascript
Array.of(3, 11, 8); // [3,11,8]
Array.of(3); // [3]
Array.of(3).length; // 1
```

这个方法的主要目的，是弥补数组构造函数`Array()`的不足。因为参数个数的不同，会导致`Array()`的行为有差异。

```javascript
Array(); // []
Array(3); // [, , ,]
Array(3, 11, 8); // [3, 11, 8]
```

上面代码中，`Array` 方法没有参数、一个参数、三个参数时，返回结果都不一样。只有当参数个数不少于 2 个时，`Array()` 才会返回由参数组成的新数组。参数个数只有一个时，实际上是指定数组的长度。

`Array.of` 基本上可以用来替代`Array()`或`new Array()`，并且不存在由于参数不同而导致的重载。它的行为非常统一。

```javascript
Array.of(); // []
Array.of(undefined); // [undefined]
Array.of(1); // [1]
Array.of(1, 2); // [1, 2]
```

`Array.of` 总是返回参数值组成的数组。如果没有参数，就返回一个空数组。

`Array.of` 方法可以用下面的代码模拟实现。

```javascript
function ArrayOf() {
  return [].slice.call(arguments);
}
```

#### 三 . copyWithin()

数组实例的`copyWithin`方法，在当前数组内部，将指定位置的成员复制到其他位置（会覆盖原有成员），然后返回当前数组。也就是说，使用这个方法，会修改当前数组 。

##### **语法**

```javascript
Array.prototype.copyWithin(target, (start = 0), (end = this.length));
```

##### 接受三个参数。

- target（必需）：从该位置开始替换数据。
- start（可选）：从该位置开始读取数据，默认为 0。如果为负值，表示倒数。
- end（可选）：到该位置前停止读取数据，默认等于数组长度。如果为负值，表示倒数。

看下面的例子 :

```javascript
let cct1 = [1, 2, 3, 4, 5].copyWithin(0, 3);
console.log(cct1); //[ 4, 5, 3, 4, 5 ]

// 将3号位复制到0号位
let cct2 = [1, 2, 3, 4, 5].copyWithin(0, 3, 4);
console.log(cct2); // [4, 2, 3, 4, 5]

// -2相当于3号位，-1相当于4号位
let cct3 = [1, 2, 3, 4, 5].copyWithin(0, -2, -1);
console.log(cct3); //[4, 2, 3, 4, 5]

// 将3号位复制到0号位
let cct4 = [].copyWithin.call({ length: 5, 3: 1 }, 0, 3);
console.log(cct4); // {0: 1, 3: 1, length: 5}

// 将2号位到数组结束，复制到0号位
let i32a = new Int32Array([1, 2, 3, 4, 5]);
let cct5 = i32a.copyWithin(0, 2);
console.log(cct5); // Int32Array [3, 4, 5, 4, 5]

// 对于没有部署 TypedArray 的 copyWithin 方法的平台
// 需要采用下面的写法
let cct6 = [].copyWithin.call(new Int32Array([1, 2, 3, 4, 5]), 0, 3, 4);
console.log(cct6); // Int32Array [4, 2, 3, 4, 5]
```

#### 四 . 数组实例的 find() 和 findIndex()

##### 1.数组实例的`find`方法，用于找出第一个符合条件的数组成员。

> 它的参数是一个回调函数，所有数组成员依次执行该回调函数，直到找出第一个返回值为`true`的成员，然后返回该成员。如果没有符合条件的成员，则返回`undefined` 。

**语法**

```javascript
arr.find(callback[, thisArg])
```

**参数**

- `callback`

  在数组每一项上执行的函数，接收 3 个参数：`element`当前遍历到的元素。`index`当前遍历到的索引。`array`数组本身。

- `thisArg`可选

  可选，指定 `callback 的 this 参数。`

看下面的例子 :

```javascript
let cct = [1, 4, -5, 10].find(n => n < 0);
console.log(cct); //-5

let cct2 = [1, 5, 10, 15].find(function(value, index, arr) {
  return value > 9;
});
console.log(cct2); // 10
```

##### 2.`findIndex`方法的用法与`find`方法非常类似，返回第一个符合条件的数组成员的位置，如果所有成员都不符合条件，则返回`-1` .

```javascript
let cct3 = [1, 5, 10, 15].findIndex(function(value, index, arr) {
  return value > 9;
});
console.log(cct3); // 2
```

**另外，这两个方法都可以发现`NaN`，弥补了数组的`IndexOf`方法的不足**。

```javascript
[NaN]
  .indexOf(NaN)
  // -1

  [NaN].findIndex(y => Object.is(NaN, y));
// 0
```

上面代码中，`indexOf`方法无法识别数组的`NaN`成员，但是`findIndex`方法可以借助`Object.is`方法做到。

#### 五 . fill()

`fill`方法使用给定值，填充一个数组。

```javascript
let cct4 = ['a', 'b', 'c'].fill(7);
console.log(cct4); // [7, 7, 7]

let cct5 = new Array(3).fill(7);
console.log(cct5); // [7, 7, 7]
```

上面代码表明，`fill`方法用于空数组的初始化非常方便。数组中已有的元素，会被全部抹去。

`fill`方法还可以接受第二个和第三个参数，用于指定填充的起始位置和结束位置。

```javascript
let cct6 = ['a', 'b', 'c'].fill(7, 1, 2);
console.log(cct6); // ['a', 7, 'c']
```

上面代码表示，`fill`方法从 1 号位开始，向原数组填充 7，到 2 号位之前结束。

#### 六 . entries()，keys() 和 values()

##### 1.`entries()` 方法返回一个新的**Array Iterator**对象，该对象包含数组中每个索引的键/值对 。

##### 2.`keys()`方法返回一个新的 Array 迭代器，它包含数组中每个索引的键。

##### 3.**values()** 方法返回一个新的 **Array Iterator** 对象，该对象包含数组每个索引的值。

```javascript
for (let index of ['a', 'b'].keys()) {
  console.log(index);
}
// 0
// 1

for (let elem of ['a', 'b'].values()) {
  console.log(elem);
}
// 'a'
// 'b'

for (let [index, elem] of ['a', 'b'].entries()) {
  console.log(index, elem);
}
// 0 'a'
// 1 'b'
```

**如果不使用`for...of`循环，可以手动调用遍历器对象的`next`方法，进行遍历。**

```javascript
let letter = ['a', 'b', 'c'];
let entries = letter.entries();
console.log(entries.next().value); // [0, 'a']
console.log(entries.next().value); // [1, 'b']
console.log(entries.next().value); // [2, 'c']
```

#### 七 . includes()

`includes()` 方法用来判断一个数组是否包含一个指定的值，如果是，返回 true, 反之则返回 false .

##### 语法

```javascript
arr.includes(searchElement);
arr.includes(searchElement, fromIndex);
```

##### 参数

- `searchElement`

  需要查找的元素值。

- `fromIndex` 可选

  从该索引处开始查找 `searchElement`。如果为负值，则按升序从 array.length + fromIndex 的索引开始搜索。默认为 0。

##### 返回值

一个 [`Boolean`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Boolean)。

```javascript
[1, 2, 3]
  .includes(2) // true
  [(1, 2, 3)].includes(4) // false
  [(1, 2, NaN)].includes(NaN); // true
```

该方法的第二个参数表示搜索的起始位置，默认为`0`。如果第二个参数为负数，则表示倒数的位置，如果这时它大于数组长度（比如第二个参数为`-4`，但数组长度为`3`），则会重置为从`0`开始。

```javascript
[1, 2, 3].includes(3, 3); // false
[1, 2, 3].includes(3, -1); // true
```

**注意 :** 没有该方法之前，我们通常使用数组的`indexOf`方法，检查是否包含某个值。

```javascript
if (arr.indexOf(el) !== -1) {
  // ...
}
```

`indexOf`方法有两个缺点，一是不够语义化，它的含义是找到参数值的第一个出现位置，所以要去比较是否不等于`-1`，表达起来不够直观。二是，它内部使用严格相等运算符（`===`）进行判断，这会导致对`NaN`的误判。

```javascript
[NaN].indexOf(NaN);
// -1
```

`includes`使用的是不一样的判断算法，就没有这个问题。

```javascript
[NaN].includes(NaN);
// true
```

下面代码用来检查当前环境是否支持该方法，如果不支持，部署一个简易的替代版本。

```javascript
const contains = (() =>
  Array.prototype.includes
    ? (arr, value) => arr.includes(value)
    : (arr, value) => arr.some(el => el === value))();
contains(['foo', 'bar'], 'baz'); // => false
```

另外，Map 和 Set 数据结构有一个`has`方法，需要注意与`includes`区分。

- Map 结构的`has`方法，是用来查找键名的，比如`Map.prototype.has(key)`、`WeakMap.prototype.has(key)`、`Reflect.has(target, propertyKey)`。
- Set 结构的`has`方法，是用来查找值的，比如`Set.prototype.has(value)`、`WeakSet.prototype.has(value)`。

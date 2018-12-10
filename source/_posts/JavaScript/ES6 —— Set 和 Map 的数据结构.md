---
title: ES6 —— Set 和 Map 的数据结构
author: Deepspace
top: true
img: https://github.com/IDeepspace/ImageHosting/raw/master/bg/2.jpg 
tags:
  - ES6
categories: JavaScript
date: 2017-01-28
urlname: es6-set-map
---

<!-- ## ES6 —— Set 和 Map 的数据结构 -->

### 一 . Set

`ES6` 提供了新的数据结构 `Set` 。它类似于数组，但是成员的值都是唯一的，没有重复的值。

`Set` 本身是一个构造函数，用来生成 `Set` 数据结构。

```javascript
const s = new Set();

[2, 3, 5, 4, 5, 2, 2].forEach(x => s.add(x));

for (let i of s) {
  console.log(i);
}
// 2
// 3
// 5
// 4
```

<!-- more -->

在 `Set` 内部，两个 `NaN` 是相等。两个对象总是不相等的。可以用 `length` 来检测 .

#### **Set 实例的属性和方法**

Set 结构的实例有以下属性。

- `Set.prototype.constructor`：构造函数，默认就是`Set`函数。
- `Set.prototype.size`：返回`Set`实例的成员总数。

Set 实例的方法分为两大类：操作方法（用于操作数据）和遍历方法（用于遍历成员）。下面先介绍四个操作方法。

- `add(value)`：添加某个值，返回 Set 结构本身。
- `delete(value)`：删除某个值，返回一个布尔值，表示删除是否成功。
- `has(value)`：返回一个布尔值，表示该值是否为`Set`的成员。
- `clear()`：清除所有成员，没有返回值。

#### 遍历操作

Set 结构的实例有四个遍历方法，可以用于遍历成员。

- `keys()`：返回键名的遍历器
- `values()`：返回键值的遍历器
- `entries()`：返回键值对的遍历器
- `forEach()`：使用回调函数遍历每个成员

需要特别指出的是，`Set`的遍历顺序就是插入顺序。这个特性有时非常有用，比如使用 Set 保存一个回调函数列表，调用时就能保证按照添加顺序调用。

### 二 . Map

Map 结构提供了“值—值”的对应，是一种更完善的 Hash 结构实现。如果你需要“键值对”的数据结构，Map 比 Object 更合适。它类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。

```javascript
var m = new Map();
var o = { p: 'Hello World' };

m.set(o, 'content');
m.get(o); // "content"

m.has(o); // true
m.delete(o); // true
m.has(o); // false
```

注意，只有对同一个对象的引用，Map 结构才将其视为同一个键。这一点要非常小心。

```javascript
var map = new Map();

map.set(['a'], 555);
map.get(['a']); // undefined
```

上面代码的 `set` 和 `get` 方法，表面是针对同一个键，但实际上这是两个值，内存地址是不一样的，因此 get 方法无法读取该键，返回 `undefined`。

如果 Map 的键是一个简单类型的值（数字、字符串、布尔值），则只要两个值严格相等，`Map` 将其视为一个键，包括 `0` 和 `-0`。另外，虽然 `NaN` 不严格相等于自身，但 `Map` 将其视为同一个键。

实例属性和方法：`size`、`set`、`get`、`has`、`delete`、`clear`

遍历方法：`keys()`、`values()`、`entries()`、`forEach()`

---
layout: post
title:  "javascript高级用法"
date:   2018-10-24
categories: js
author: 张翔
location: ShangHai, China
cover: ""
description: 最近开发使用到很多不同的javascript的特性和用法，记录一下!
---
---
## 一、Map的使用
最近开发和用js刷leetcode的时候，map和set都很有用，下面面总结一下map和set的用法。   

####Objects 和 maps 的比较
 - 一个Object的键只能是字符串或者 Symbols，但一个 Map 的键可以是任意值，包括函数、对象、基本类型。
 - Map 中的键值是有序的，而添加到对象中的键则不是。因此，当对它进行遍历时，Map对象是按插入的顺序返回键值。
 - 你可以通过 size 属性直接获取一个 Map 的键值对个数，而 Object 的键值对个数只能手动计算。
 - Map 可直接进行迭代，而 Object 的迭代需要先获取它的键数组，然后再进行迭代。
 - Object 都有自己的原型，原型链上的键名有可能和你自己在对象上的设置的键名产生冲突。虽然 ES5 开始可以用 map = Object.create(null) 来创建一个没有原型的对象，但是这种用法不太常见。
 - Map 在涉及频繁增删键值对的场景下会有些性能优势。

1.实例化map
```javascript
let map = new Map();
//构造函数创建，map会保持键的唯一：Map { 'a' => 3, 'b' => 2 }
let map = new Map([['a',1],['b',2],['a',3]]);
let cloneMap = new Map(map)
```


2.增删改查
```javascript
// 添加键
map.set('a', 'b');
map.set(NaN, "not a number");
// map的大小
map.size; // 2
// 读取
map.get('a');
// 删除
map.delete(NaN);
// 清空
map.clear();
// 是否包含键对应的值
map.has(NaN);
// 大小
map.size;
```
3.迭代，map.keys()和map.values()和map.entries()都不是数组，要转换为数组看下面
```javascript
// 可使用for..of迭代Map
for (let [key, value] of map) {
    console.log(key + " = " + value);
}
// 迭代key
for (let key of map.keys()) {
    console.log(key);
}
// 迭代value
for (let value of map.values()) {
    console.log(value);
}
// 迭代实体
for (let [key, value] of map.entries()) {
    console.log(key + " = " + value);
}
// forEach循环
map.forEach(function(value, key) {
    console.log(key + " = " + value);
});
```
4.转换为数组
```javascript
// map转换数组[ [ 'a', 'b' ], [ NaN, 'not a number' ] ]
Array.from(map);
// key转换数组[ 'a', NaN ]
Array.from(map.keys());
```

## 二、Set的使用
 Set可以存储**任何类型**的**唯一值**。
 
1.实例化set
```javascript
let set = new Set();
let func = function () {}
// 构造函数创建set，可以存储任何值，且是唯一的
let set2 = new Set([1, NaN, func, 1]); // Set { 1, NaN, [Function: func] }
// string构造
let stringSet = new Set('chinese');  // Set { 'c', 'h', 'i', 'n', 'e', 's' }
```
2.增删改查
```javascript
// 添加值数字、字符串、对象
set.add(1);
set.add("some text");
let o = {a: 1, b: 2};
set.add(o);
// 删除
set.delete(o);
// 清空
set.clear();
// 是否包含键对应的值
set.has(1);
// 长度
console.log(set.size);
```
3.迭代
```javascript
// 迭代
for (let item of set) console.log(item);
for (let item of set.keys()) console.log(item);
for (let item of set.values()) console.log(item);
for (let [key, value] of set.entries()) console.log(key,value);
// 转换Set为Array
[v for (v of set)]; //[ 1, 'some text', { a: 1, b: 2 } ]
// 替代方案
Array.from(set); //[ 1, 'some text', { a: 1, b: 2 } ]
```
4.实现集合的基本操作
```javascript

// 是否是子集
function isSuperset(set, subset) {
    for (let elem of subset) {
        if (!set.has(elem)) {
            return false;
        }
    }
    return true;
}
// 并集
function union(setA, setB) {
    let _union = new Set(setA);
    for (let elem of setB) {
        _union.add(elem);
    }
    return _union;
}
// 交集
function intersection(setA, setB) {
    let _intersection = new Set();
    for (let elem of setB) {
        if (setA.has(elem)) {
            _intersection.add(elem);
        }
    }
    return _intersection;
}
// 差集
function difference(setA, setB) {
    let _difference = new Set(setA);
    for (let elem of setB) {
        _difference.delete(elem);
    }
    return _difference;
}
let setA = new Set([1, 2, 3, 4]),
    setB = new Set([2, 3]),
    setC = new Set([3, 4, 5, 6]);

isSuperset(setA, setB); // => true
union(setA, setC); // => Set [1, 2, 3, 4, 5, 6]
intersection(setA, setC); // => Set [3, 4]
difference(setA, setC); // => Set [1, 2]
```

## 三、ES6、ES7、ES8中比较有用的方法

**1.Array.prototype.includes()**
`includes`函数与 `indexOf` 函数很相似，下面两个表达式是等价的：
```javascript
arr.includes(x)
arr.indexOf(x) >= 0
```
**2.Object.values()**
`Object.values()`是一个与`Object.keys()`类似的新函数，但返回的是Object自身属性的所有值，不包括继承的值。

**3.Object.entries**
`Object.entries()`函数返回一个给定对象自身可枚举属性的键值对的数组。
```javascript
for(let [key,value] of Object.entries(obj1)){
    console.log(`key: ${key} value:${value}`)
}
//key:a value:1
//key:b value:2
//key:c value:3
```
**4.String padding**
在ES8中String新增了两个实例函数`String.prototype.padStart`和`String.prototype.padEnd`，允许将空字符串或其他字符串添加到原始字符串的开头或结尾。填充字符串的用例包括：

 - 以等宽字体显示平整的数据。
 - 在文件名或URL中添加计数或ID：’file 001.txt’。
 - 对齐控制台输出： ‘Test 001: ✓’。
 - 打印具有固定位数的十六进制或二进制数字：’0x00FF’。

## 四、比较两个数组完全相同
```javascript
// 方法1：先两个数组用同样的方式排序，再字符串化比较
// 方法2：把两个数组分别放到Set里面去，再把其中一个Set add到另外一个Set，如果长度没变两数组元素相同
```

## 五、replaceAll方法
js里面没有replaceAll方法，直接正则表达式/g

```javascript
// 比如去掉金额格式化
s.replace(/,/g, '');
```

## 六、金额格式化和去格式化
金额格式化为例如10,111,111.10

```javascript
// 金额格式化
function formatMoney(s) {
    // 保留2位小数
    let n = 2;
    s = parseFloat((s + '').replace(/[^\d.-]/g, '')).toFixed(n) + '';
    let l = s.split('.')[0].split('').reverse();
    let r = s.split('.')[1];
    let t = '';
    for (let i = 0; i < l.length; i++) {
      t += l[i] + ((i + 1) % 3 === 0 && (i + 1) !== l.length ? ',' : '');
    }
    return t.split('').reverse().join('') + '.' + r;
  }
```
```javascript
// 金额去格式化
function removeFormatMoney(s) {
    return parseFloat(s.replace(/,/g, ''));
}
```
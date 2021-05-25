# JS 数据类型

## 原始数据类型

boolean
null
undefined
number
string
symbol
bigint

其中 JS 的数字类型是浮点类型的，没有整型。并且浮点类型基于 IEEE 754标准实现，在使用中会遇到某些 Bug。NaN 也属于 number 类型，并且 NaN 不等于自身。
对于基本类型来说，如果使用字面量的方式，那么这个变量只是个字面量，只有在必要的时候才会转换为对应的类型

```js 
let mu = 111  // 这只是字面量，不是 number 类型
mu.toString() // 使用时候才会转换为对象类型
```

## 引用数据类型
对象Object（包含普通对象-Object，数组对象-Array，正则对象-RegExp，日期对象-Date，数学函数-Math，函数对象-Function）
对象是最复杂的数据类型，又可以分成三个子类型。
- 狭义的对象（object）
- 数组（array）
- 函数（function）

狭义的对象和数组是两种不同的数据组合方式，除非特别声明，本教程的“对象”都特指狭义的对象。
函数其实是处理数据的方法，JavaScript 把它当成一种数据类型，可以赋值给变量，这为编程带来了很大的灵活性，也为 JavaScript 的“函数式编程”奠定了基础。

对象（Object）是引用类型，在使用过程中会遇到浅拷贝和深拷贝的问题。
```js 
let a = { name: 'FE' }
let b = a
b.name = 'EF'
console.log(a.name) // EF
```
## 判断类型
JavaScript 有三种方法，可以确定一个值到底是什么类型。
- typeof运算符
- instanceof运算符
- Object.prototype.toString方法

### Typeof
typeof运算符可以返回一个值的数据类型。
数值、字符串、布尔值分别返回number、string、boolean。
```js
typeof 123 // "number"
typeof '123' // "string"
typeof false // "boolean"
```

函数返回function。
```js
function f() {}
typeof f
// "function"
```

undefined返回undefined。
```js
typeof undefined
// "undefined"
// 利用这一点，typeof可以用来检查一个没有声明的变量，而不报错。
// 实际编程中，这个特点通常用在判断语句
// 错误的写法
if (v) {
  // ...
}
// ReferenceError: v is not defined

// 正确的写法
if (typeof v === "undefined") {
  // ...
}
```
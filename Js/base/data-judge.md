# 判断类型
JavaScript 有三种方法，可以确定一个值到底是什么类型。
- typeof运算符
- instanceof运算符
- Object.prototype.toString方法

## Typeof
typeof运算符可以返回一个值的数据类型。
typeof 对于基本类型，除了 null 都可以显示正确的类型
- 识别所有值类型
- 识别函数
- 判断是否是引用类型（不可再细分）

数值、字符串、布尔值分别返回number、string、boolean。
```js
typeof 123 // "number"
typeof NaN // "number"
typeof '123' // "string"
typeof false // "boolean"
typeof b // b 没有声明，但是还会显示 undefined
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

对象返回object。
null返回object。
```js
typeof window // "object"
typeof {}     // "object"
typeof []     // "object"
typeof console.log // 'function'
typeof null // "object"
```
null的类型是object，这是由于历史原因造成的。1995年的 JavaScript 语言第一版，只设计了五种数据类型（对象、整数、浮点数、字符串和布尔值），没考虑null，只把它当作object的一种特殊值。后来null独立出来，作为一种单独的数据类型，为了兼容以前的代码，typeof null返回object就没法改变了。
在 JS 的最初版本中，使用的是 32 位系统，为了性能考虑使用低位存储了变量的类型信息，000 开头代表是对象，然而 null 表示为全零，所以将它错误的判断为 object 。虽然现在的内部类型判断代码已经改变了，但是对于这个 Bug 却是一直流传下来。

``` js
let a
// 我们也可以这样判断 undefined
a === undefined
// 但是 undefined 不是保留字，能够在低版本浏览器被赋值
let undefined = 1
// 这样判断就会出错
// 所以可以用下面的方式来判断，并且代码量更少
// 因为 void 后面随便跟上一个组成表达式
// 返回就是 undefined
a === void 0
```

## instanceof

## Object.prototype.toString
如果我们想获得一个变量的正确类型，可以通过 Object.prototype.toString.call(xx)。这样我们就可以获得类似 [object Type] 的字符串。


# 类型转换
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
对于引用数据类型，采用typeof判断对象数据类型是不合适的，采用instanceof会更好，instanceof的原理是基于原型链的查询，只要处于原型链中，判断永远为true

## Number.isNaN() 、 isNaN()

typeof + Number.isNaN()准确判断数字类型
``` js
function _isNum(num) {
  return typeof num === 'number' && !Number.isNaN(num)
}

```
Number.isNaN()：用于确定传递的值是否为 NaN，并且检查其类型是否为 Number
isNaN()：也是用来判断值是否为NaN，和上面不同的是，如果传递的值不是number类型，会使用Number()进行强转后判断。来试着实现一个polyfill：
``` js
var isNaN = isNaN || function (v) {
    var n = Number(v)
    return n !== n
}

```
上述中使用了NaN的一个特性，就是自身永远不等于自身。

用isNaN()实现一个Number.isNaN()：
``` js
Number.isNaN = Number.isNaN || function(v) {
  return typeof v === 'number' && isNaN(v)
}

```

## instanceof
用来判断左边的对象实例的原型链上是否存在右边构造函数的prototype
``` js
let date = new Date()
date instanceof Date // true
date instanceof Object // true

let fn = () => {}
fn instanceof Function // true
fn instanceof Object // true

let arr = []
arr instanceof Array // true
arr instanceof Object // true

let o = {}
o instanceof Object // true

class A {}
let a = new A()
a instanceof A // true
a instanceof Object // true
class A_1 extends A {}
let a_1 = new A_1()
a_1 instanceof A_1 // true
a_1 instanceof A // true
a_1 instanceof Object // true

```

通过上面的代码，总结一下规律，我们来实现一个简版的instanceof：
``` js
Object.prototype._instanceof = function (constru) {
    let consProto = constru.prototype
    let currentProto = this.__proto__
    
    while(true) {
        if (currentProto === null) return false
        if (currentProto === consProto) return true
        currentProto = currentProto.__proto__
    }
}

```
注意： Object.prototype.__proto__ 虽然被广大浏览器支持，且此属性已被ES6规范中标准化，但是规范里仍不建议使用此属性，我们可以使用Object.getPrototypeOf()代替它。
为什么instanceof 判断不了基本类型？
``` js
let n  = 2
n instanceof Number  // false
n instanceof Object // false

let n_n = new Number(2)
n_n instanceof Number // true
n_n instanceof Object // true

let str = 'Madman'
str instanceof String // false
str instanceof Object // false

let str_str = new String('Madman')
str_str instanceof String // true
str_str instanceof Object // true

```

我们知道创建变量常用的两种方式：一个是字面量，一个是通过构造函数。

像直接通过字面量声明的变量我们称之为“原始值”，原始值是没有任何属性和方法的，只有当起被使用时才会被装箱。所以一般不使用instanceof 判断这种基本数据类型。

## Object.prototype.toString
如果我们想获得一个变量的正确类型，可以通过 Object.prototype.toString.call(xx)。这样我们就可以获得类似 [object Type] 的字符串。


# 类型转换
- 字符串拼接
- ==
- if语句和逻辑运算

## 字符串拼接
``` js
const a = 100 + 10 //110
const b = 100 + '10' // '10010'
const c = true + '10'  // 'true10'
```

## ==
``` js
100 == '100' // true
0 == ''     // true
0 == false  // true
false == '' // true
null == undefined // true
```


# 判断类型
JavaScript 有三种方法，可以确定一个值到底是什么类型。
- typeof运算符
- instanceof运算符
- Object.prototype.toString方法

## Typeof
typeof运算符可以返回一个值的数据类型。

typeof是一个运算符，有2种使用方式：typeof(表达式)和typeof 变量名，第一种是对表达式做运算，第二种是对变量做运算。typeof 操作符返回一个字符串，表示未经计算的操作数的类型。

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

### 手写typeof

typeof可以正确识别Undefined、Boolean、Number、String、Symbol、Function等数据类型，但是在数组，日期，对象上的判断确并不准确，都是返回object

优化版本实现代码
``` js
/*
实现思路：
1.利用Object.prototype.toString()方法
2.将Object.prototype.toString()方法获取的字符串进行截取
3.利用toLowerCase()方法，将字符串转为小写
*/

function typeOf(obj) {    
	return Object.prototype.toString.call(obj).slice(8,-1).toLowerCase();
}

typeOf([]); 		// 输出 array 
typeOf({}); 		// 输出 object
typeOf(new Date); 	// 输出 date
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
JS中，类型转换只有三种：
- 转换成数字
- 转换成布尔值
- 转换成字符串

转换方式
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
除了 == null之外，其他都一律用 === 
``` js
const obj = { x: 100}
if(obj.a == null){}
// 相当于
// if(obj.a === null || obj.a === undefined{})
```

### [] == ![]结果是什么？为什么？
解析:
== 中，左右两边都需要转换为数字然后进行比较。
[]转换为数字为0。
![] 首先是转换为布尔值，由于[]作为一个引用类型转换为布尔值为true,
因此![]为false，进而在转换成数字，变为0。
0 == 0 ， 结果为true

## if语句和逻辑运算
- truly变量： !!a === true 的变量
- falsely变量： !!a === false 的变量

以下是falsely变量。 除此之外都是truly变量
``` js
!!0 === false
!!NaN === false
!!'' === false
!!null === false
!!undefined === false
!!false === false
```

逻辑判断
``` js
console.log(10 && 0)   //0
console.log('' || 'abc')  // 'abc'
console.log(!window.abc)  // true
```

## 字符串转换
当我们需要一个字符串形式的值时，就会进行字符串转换。

比如，alert(value) 将 value 转换为字符串类型，然后显示这个值。

我们也可以显式地调用 String(value) 来将 value 转换为字符串类型：

``` js
let value = true;
alert(typeof value); // boolean

value = String(value); // 现在，值是一个字符串形式的 "true"
alert(typeof value); // string
```

## 数字型转换
在算术函数和表达式中，会自动进行 number 类型转换。

比如，当把除法 / 用于非 number 类型：
``` js
alert( "6" / "2" ); // 3, string 类型的值被自动转换成 number 类型后进行计算
```
我们也可以使用 Number(value) 显式地将这个 value 转换为 number 类型。
``` js
let str = "123";
alert(typeof str); // string

let num = Number(str); // 变成 number 类型 123

alert(typeof num); // number
```

number 类型转换规则：

|  值   | 变成  |
|  ----  | ----  |
| undefined  | NaN |
| null  | 0 |
| true 和 false  | 1 和 0 |
| string  | 去掉首尾空格后的纯数字字符串中含有的数字。如果剩余字符串为空，则转换结果为 0。否则，将会从剩余字符串中“读取”数字。当类型转换出现 error 时返回 NaN。 |

``` js
alert( Number("   123   ") ); // 123
alert( Number("123z") );      // NaN（从字符串“读取”数字，读到 "z" 时出现错误）
alert( Number(true) );        // 1
alert( Number(false) );       // 0
```

请注意 null 和 undefined 在这有点不同：null 变成数字 0，undefined 变成 NaN。

大多数数学运算符也执行这种转换，我们将在下一节中进行介绍。

## 布尔型转换
布尔（boolean）类型转换是最简单的一个。

它发生在逻辑运算中（稍后我们将进行条件判断和其他类似的东西），但是也可以通过调用 Boolean(value) 显式地进行转换。

转换规则如下：
- 直观上为“空”的值（如 0、空字符串、null、undefined 和 NaN）将变为 false。
- 其他值变成 true。
请注意：包含 0 的字符串 "0" 是 true

一些编程语言（比如 PHP）视 "0" 为 false。但在 JavaScript 中，非空的字符串总是 true。
```js
alert( Boolean("0") ); // true
alert( Boolean(" ") ); // 空白，也是 true（任何非空字符串都是 true）
```
布尔型转换遵循以下规则：
|  值   | 变成  |
|  ----  | ----  |
| 0, null, undefined, NaN, ""  | false |
| 其他值  | true |

在条件判断时，除了 undefined， null， false， NaN， ''， 0， -0，其他所有值都转为 true，包括所有对象。

## 总结

有三种常用的类型转换：转换为 string 类型、转换为 number 类型和转换为 boolean 类型。

字符串转换 —— 转换发生在输出内容的时候，也可以通过 String(value) 进行显式转换。原始类型值的 string 类型转换通常是很明显的。

数字型转换 —— 转换发生在进行算术操作时，也可以通过 Number(value) 进行显式转换。


上述的大多数规则都容易理解和记忆。人们通常会犯错误的值得注意的例子有以下几个：

对 undefined 进行数字型转换时，输出结果为 NaN，而非 0。
对 "0" 和只有空格的字符串（比如：" "）进行布尔型转换时，输出结果为 true。


## 对象转基本类型
对象在转换基本类型时，首先会调用 valueOf 然后调用 toString。并且这两个方法你是可以重写的。
```js
let a = {
  valueOf(){
    return 0
  }
}
```
当然你也可以重写 Symbol.toPrimitive ，该方法在转基本类型时调用优先级最高。
``` js
let a = {
  valueOf() {
    return 0;
  },
  toString() {
    return '1';
  },
  [Symbol.toPrimitive]() {
    return 2;
  }
}
1 + a // => 3
'1' + a // => '12'
```




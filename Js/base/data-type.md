# JS 数据类型

## 原始数据类型
JS 中分为七种内置类型，七种内置类型又分为两大类型：原始数据类型和引用数据类型。

原始数据类型有七种：
+ boolean
+ null
+ undefined
+ number
+ string
+ symbol
+ bigint

其中 JS 的数字类型是浮点类型的，没有整型。并且浮点类型基于 IEEE 754标准实现，在使用中会遇到某些 Bug。NaN 也属于 number 类型，并且 NaN 不等于自身。
对于基本类型来说，如果使用字面量的方式，那么这个变量只是个字面量，只有在必要的时候才会转换为对应的类型

```js 
let mu = 111  // 这只是字面量，不是 number 类型
mu.toString() // 使用时候才会转换为对象类型
```
### null和undefined
null与undefined都可以表示“没有”，含义非常相似。将一个变量赋值为undefined或null，老实说，语法效果几乎没区别。
```js
var a = undefined;
// or
var a = null;
```
上面代码中，变量a分别被赋值为undefined和null，这两种写法的效果几乎等价。
在if语句中，它们都会被自动转为false，相等运算符（==）甚至直接报告两者相等。

1995年 JavaScript 诞生时，最初像 Java 一样，只设置了null表示"无"。根据 C 语言的传统，null可以自动转为0。
```js
Number(null)  // 0
5 + null      // 5
```

#### 区别
两者区别是这样的：null是一个表示“空”的对象，转为数值时为0；undefined是一个表示"此处无定义"的原始值，转为数值时为NaN。
```js
Number(undefined) // NaN
5 + undefined // NaN
```

#### 用法和含义
null表示空值，即该处的值现在为空。调用函数时，某个参数未设置任何值，这时就可以传入null，表示该参数为空。比如，某个函数接受引擎抛出的错误作为参数，如果运行过程中未出错，那么这个参数就会传入null，表示未发生错误。

undefined表示“未定义”，下面是返回undefined的典型场景。
``` js
// 变量声明了，但没有赋值
var i;
i // undefined

// 调用函数时，应该提供的参数没有提供，该参数等于 undefined
function f(x) {
  return x;
}
f() // undefined

// 对象没有赋值的属性
var  o = new Object();
o.p // undefined

// 函数没有返回值时，默认返回 undefined
function f() {}
f() // undefined
```

### 布尔值
布尔值代表“真”和“假”两个状态。“真”用关键字true表示，“假”用关键字false表示。布尔值只有这两个值。

下列运算符会返回布尔值：
> - 前置逻辑运算符： ! (Not)
> - 相等运算符：\=\=\=，\!=\=，==，\!=
> - 比较运算符：>，>=，<，<=

如果 JavaScript 预期某个位置应该是布尔值，会将该位置上现有的值自动转为布尔值。转换规则是除了下面六个值被转为false，其他值都视为true。
> - undefined
> - null
> - false
> - 0
> - NaN
> - ""或''（空字符串）

>
> 注意，空数组（[]）和空对象（{}）对应的布尔值，都是true。
>


### number数值
#### 整数和浮点数
JavaScript 内部，所有数字都是以64位浮点数形式储存，即使整数也是如此。所以，1与1.0是相同的，是同一个数。
> 1 === 1.0 // true

这就是说，JavaScript 语言的底层根本没有整数，所有数字都是小数（64位浮点数）。容易造成混淆的是，某些运算只有整数才能完成，此时 JavaScript 会自动把64位浮点数，转成32位整数，然后再进行运算，参见《运算符》一章的“位运算”部分。

由于浮点数不是精确的值，所以涉及小数的比较和运算要特别小心。
``` js
0.1 + 0.2 === 0.3
// false

0.3 / 0.1
// 2.9999999999999996

(0.3 - 0.2) === (0.2 - 0.1)
// false
```
## 引用数据类型
对象Object（包含普通对象-Object，数组对象-Array，正则对象-RegExp，日期对象-Date，数学函数-Math，函数对象-Function）
对象是最复杂的数据类型，又可以分成三个子类型。
- 狭义的对象（object）
- 数组（array）
- 函数（function）

引用类型Object： Array ， Function ， Date ， RegExp 等
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
typeof 对于基本类型，除了 null 都可以显示正确的类型

数值、字符串、布尔值分别返回number、string、boolean。
```js
typeof 123 // "number"
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

### instanceof

### Object.prototype.toString
如果我们想获得一个变量的正确类型，可以通过 Object.prototype.toString.call(xx)。这样我们就可以获得类似 [object Type] 的字符串。


## 类型转换
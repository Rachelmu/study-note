# array 数组

## 创建与初始化

new Array
``` js
const array = new Array('apple','banana')
```

字面量
```js
const array = ['apple', 'banana']
```
在es6中引入了两个新方法，同样可以创建数组：
+ Array.of() 返回由所有参数组成的数组，不考虑参数的数量或类型，如果没有参数就返回一个空数组
+ Array.from()从一个类数组或可迭代对象中创建一个新的数组

这两个方法分别解决了两个问题，Array.of()解决了构造函数方法创建数组时单个数字引起了怪异行为。
Array.from()解决了『类数组』的转化问题，之前我们将类数组转化为数组的方法普遍用的是Array.prototype.slice.call(arguments)这种偏Hack的方法，Array.from()的出现将其规范化，在以后的转化中我们最好按照标准的Array.from()方法进行转化。
类数组转换

```js
const a = new Array(3);   // (3) [empty × 3] 构造函数方法单个数组会被用于数组长度
const b = Array.of(3);    // [3]
```

## 数组的操作

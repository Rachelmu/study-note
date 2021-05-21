# Object.keys
ES5 引入了Object.keys方法，返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键名。

```js
const obj = {
  100: '一百',
  2: '二',
  7: '七'
}
Object.keys(obj) // ["2", "7", "100"]

const obj = {
  c: 'c',
  a: 'a',
  b: 'b'
}
Object.keys(obj) // ["c", "a", "b"]

```

## Object.keys 属性排序规律

Object.keys在内部会根据属性名key的类型进行不同的排序逻辑。分三种情况：
- 如果属性名的类型是Number，那么Object.keys返回值是按照key从小到大排序
- 如果属性名的类型是String，那么Object.keys返回值是按照属性被创建的时间升序排序。
- 如果属性名的类型是Symbol，那么逻辑同String相同

## 当Object.keys被调用时背后发生了什么

当Object.keys函数使用参数O调用时，会执行以下步骤：

第一步：将参数转换成Object类型的对象。

第二步：通过转换后的对象获得属性列表properties。

第三步：将List类型的属性列表properties转换为Array得到最终的结果。

::: tips
规范中是这样定义的：

调用ToObject(O)将结果赋值给变量obj
调用EnumerableOwnPropertyNames(obj, "key")将结果赋值给变量nameList
调用CreateArrayFromList(nameList)得到最终的结果
:::

## 获得属性列表（EnumerableOwnPropertyNames(obj, "key")）
而OrdinaryOwnPropertyKeys是这样规定的：

- 声明变量keys值为一个空列表（List类型）
- 把每个Number类型的属性，按数值大小升序排序，并依次添加到keys中
- 把每个String类型的属性，按创建时间升序排序，并依次添加到keys中
- 把每个Symbol类型的属性，按创建时间升序排序，并依次添加到keys中
- 将keys返回（return keys）

上面这个规则不光规定了不同类型的返回顺序，还规定了如果对象的属性类型是数字，字符与Symbol混合的，那么返回顺序永远是数字在前，然后是字符串，最后是Symbol。



## 将List类型转换为Array得到最终结果（CreateArrayFromList( elements )）


现在我们已经得到了一个对象的属性列表，最后一步是将List类型的属性列表转换成Array类型。
将List类型的属性列表转换成Array类型非常简单：

1.先声明一个变量array，值是一个空数组
2.循环属性列表，将每个元素添加到array中
3.将array返回


上面介绍的排序规则同样适用于下列API：

Object.entries
Object.values
for...in循环
Object.getOwnPropertyNames
Reflect.ownKeys
注意：以上API除了Reflect.ownKeys之外，其他API均会将Symbol类型的属性过滤掉。

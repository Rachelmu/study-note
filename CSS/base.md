# CSS 基础复习

## css权重
!important > 行内样式 > ID选择器 > 类选择器 > 元素选择器 > 通配符选择器 > 继承 > 浏览器默认属性。

如果两组选择器都定位到同一元素，且对同一属性设置不同的样式。则需要分别计算两组选择器的权重来确定优先级。一个选择器的优先级可以说是由四个部分计算得分，比较时按位比较。
千位：如果声明在 style 的属性则该位得一分。百位：选择器中包含ID选择器则该位得一分。
十位：选择器中包含类选择器、属性选择器或者伪类则该位得一分。
个位：选择器中包含标签选择器、伪元素选择器则该位得一分。
比如 #header p.content.active 得分为 0121。.header p.active::before 的权重是 0022。前者权重大于后者。

## 盒子模型
### 标准盒子模型
W3C的标准盒子模型：content-box
content-box内容盒模型：宽度=内容的宽度，如果后期添加了border或padding就会使盒子向外扩张
### IE盒子模型
IE盒子模型：border-box边框盒模型
border-box边框盒模型：设置的宽度就等于=内容宽度+padding+border；添加的padding和border就在原先设置的width里，border和padding越大就会向内部content扩张，导致content变小
### flex弹性盒模型
使用flex布局的容器，它内部的元素自动成为flex项目（flex item）。容器拥有两根隐形的轴，水平的主轴（main axis），和竖直的交叉轴。它的使用场景主要是屏幕自适应布局和取代浮动布局。

## flex

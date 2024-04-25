# 手写CSS

## css居中
水平垂直居中有很多方法。这里我就简要说四种
### 使用flex布局设置居中。'
容器设置
``` css
display: flex; align-items: center; `justify-content: center;`
```
使用flex 时也能通过给子项设置margin: auto实现居中。给容器设置 display: flex; 子项设置 margin: auto;
使用绝对定位的方式实现水平垂直居中。容器设置position: relative。
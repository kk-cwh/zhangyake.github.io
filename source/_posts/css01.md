title: CSS笔记
date: 2016-10-06 16:11:52
tags: CSS
original: false
comments: true
categories: 前端
thumbnail: http://7xqeyw.com1.z0.glb.clouddn.com/515151842224185402.jpg
---
#### CSS 样式基本知识
- 直接写在html标签中的样式 ，**内联式css样式**   eg：

```
 <p style="color:red">这里文字是红色。</p
```

- 写在当前文件中的样式，**嵌入式css样式**   eg：

```
<style type="text/css">
span{
color:red;
}
</style>
```
<!-- more -->
- 写在单独的文件中的样式，**外部式css样式** eg：

```
<link href="main.css" rel="stylesheet" type="text/css" />
```
- 三种样式是有优先级的，他们的优先级：内联式 > 嵌入式 > 外部式（总结来说，就是--就近原则）
但是嵌入式>外部式有一个前提：嵌入式css样式的位置一定在外部式的后面

#### CSS 选择器
-  css样式定义中在{}之前的部分就是“选择器”，“选择器”指明了{}中的“样式”作用于网页中的哪些元素
-  标签选择器  就是选择html代码中的标签  `<html>、<body>、<h1>、<p>、<img>`等

```
html标签{css样式代码;}
```
- 类选择器         `为标签设置  class="类名称"`   `英文圆点开头`

```
.class类名称{css样式代码;}
```
- ID选择器器           `为标签设置id="ID名称" 英文井号开头`

```
#ID名称{css样式代码;}
```
- 子选择器  大于符号(>),用于选择指定标签元素的第一代子元素(儿子s女儿们)

```
ul > li  {border:1px solid red;} ul下的子元素li（第一代子元素）加入红色实线边框。
```
- 包含(后代)选择器 加入空格,用于选择指定标签元素下的后辈元素

```
ul > li  {border:1px solid red;} ul下的所有子元素li（所有子后代元素）加入红色实线边框。
```
- 子选择器（child selector）仅是指它的直接后代，或者你可以理解为作用于子元素的第一代后代。而后代选择器是作用于所有子后代元素。**> 作用于元素的第一代后代，空格作用于元素的所有后代 **
- 通用选择器  使用一个（*）号指定，它的作用是匹配html中所有标签元素

```
* {color:red;} // html中任意标签元素字体颜色全部设置为红色
```
- **伪类选择符** `允许给html不存在的标签（标签的某种状态）设置样式`，比如说我们给html中一个标签元素的鼠标滑过的状态来设置字体颜色 `a:hover{color:red;}`
- 分组选择符 `为html中多个标签元素设置同一个样式`时，可以使用分组选择符（，）

```
h1,span{color:red;}  
它相当于下面两行代码：
h1{color:red;}
span{color:red;}
```
#### CSS的继承 层叠 和特殊性
- 继承 - CSS的某些样式是具有继承性的，继承就是子标签继承了上级标签的CSS样式的属性
- 特殊性 - 为同一个元素设置了不同的CSS样式代码，那么元素会启用哪一个CSS样式 ，浏览器是根据权值来判断使用哪种css样式的，权值高的就使用哪种css样式 ，权值的规则 ：标签的权值为1，类选择符的权值为10，ID选择符的权值最高为100
- 层叠 - 就是在html文件中对于同一个元素可以有多个css样式存在，当有相同权重的样式存在时，会根据这些css样式的前后顺序来决定，处于最后面的css样式会被应用
- 重要性 有些特殊的情况需要为某些样式设置具有最高权值，可以使用!important来解决  eg：`p{color:red!important;}`

#### CSS格式化排版
- 文字排版

```
 {
  font-family:'宋体';// 字体
  font-size:14px; // 字号
  color:#fff; // 颜色
  font-weight:bold; // 粗体
  font-style:italic; // 斜体
  text-decoration:underline;// 下划线
  text-decoration:line-through; // 删除线
 }
```
- 段落排版

```
{
 text-indent:2em;  // 缩进
 line-height:1.5em;// 行间距（行高）
 letter-spacing:50px;// 中文字间距、字母间距
 word-spacing:50px;// 单词间距设置
 text-align:center;// 对齐
}
```
#### 元素分类
- 块级元素 在html中`<div>、 <p>、<h1>、<form>、<ul> 和 <li>`就是块级元素。设置display:block就是将元素显示为块级元素， `每个块级元素都从新的一行开始，并且其后的元素也另起一行元素的高度、宽度、行高以及顶和底边距都可设置。元素宽度在不设置的情况下，是它本身父容器的100%（和父元素的宽度一致），除非设定一个宽度`。
- `<span>、<a>、<label>、 <strong> 和<em>`就是典型的内联元素（行内元素）（inline）元素。当然块状元素也可以通过代码display:inline将元素设置为内联元素，`内联元素和其他元素都在一行上；元素的高度、宽度及顶部和底部边距不可设置；元素的宽度就是它包含的文字或图片的宽度，不可改变`
- 内联块状元素（inline-block）就是同时具备内联元素、块状元素的特点，代码 `display:inline-block` 就是将元素设置为内联块状元素，`和其他元素都在一行上；元素的高度、宽度、行高以及顶和底边距都可设置`
- 当为元素（不论之前是什么类型元素，display:none 除外）设置以下 2 个句之一：`position : absolute;  float : left 或 float:right` 简单来说，只要html代码中出现以上两句之一，元素的display显示类型就会自动变为以 display:inline-block（块状元素）的方式显示，就可以设置元素的 width 和 height 

#### 盒子模型
![enter image description here](http://img.mukewang.com/543b4cae0001b34304300350.jpg)
![enter image description here](http://img.mukewang.com/539fbb3a0001304305570259.jpg)

#### css布局模型
1. 流动模型（Flow） 流动（Flow）是默认的网页布局模型
*在流动模型下，块状元素都会在所处的包含元素内自上而下按顺序垂直延伸分布，因为在默认状态下，块状元素的宽度都为100%。实际上，块状元素都会以行的形式占据位置*
*在流动模型下，内联元素都会在所处的包含元素内从左到右水平分布显示*
2. 浮动模型 (Float)  float:left; float:right;
任何元素在默认情况下是不能浮动的，但可以用 CSS 定义为浮动 比如 float:left;
3. 层模型（Layer） 层模型有三种形式：
- 绝对定位(position: absolute)
设置position:absolute(表示绝对定位)，这条语句的作用将元素从文档流中拖出来，然后使用left、right、top、bottom属性相对于其最接近的一个具有定位属性的父包含块进行绝对定位。如果不存在这样的包含块，则相对于body元素，即相对于浏览器窗口。
- 相对定位(position: relative)
设置position:relative（表示相对定位），它通过left、right、top、bottom属性确定元素在正常文档流中的偏移位置。`相对定位`完成的过程是首先按static(float)方式生成一个元素(并且元素像层一样浮动了起来)，然后相对于以前的位置移动，移动的方向和幅度由left、right、top、bottom属性确定，`偏移前的位置保留不动`
- 固定定位(position: fixed)
设置position:fixed 表示固定定位，它的相对移动的坐标是视图（屏幕内的网页窗口）本身。由于视图本身是固定的，它不会随浏览器窗口的滚动条滚动而变化，除非你在屏幕中移动浏览器窗口的屏幕位置，或改变浏览器窗口的显示大小，因此固定定位的元素会始终位于浏览器窗口内视图的某个位置，不会受文档流动影响
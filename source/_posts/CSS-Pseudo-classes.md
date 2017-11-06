---
title: CSS选择器详解 之 伪类
date: 2015-11-28 19:09:41
tags: CSS
categories: Web前端 
---
伪类对大小写不敏感
## 结构伪类选择器
结构伪类是CSS3新增的类型选择器，利用DOM树实现元素过滤，通过文档结构的相互关系来匹配元素，可以减少class和id属性的定义，使文档变得更加简洁。

<!-- more -->
### :root
选择元素所在文档的根元素。在(X)HTML文档中，根元素始终是html元素。
### :not（）
否定选择器，和jQuery中的:not选择器一模一样，可以选择除某个元素之外的所有元素。
e.g.
HTML：
```
<body>
    <div id="header">页头</div>
    <div id="page">页体</div>
    <div id="footer">页脚</div>
</body>
```
CSS :
```css
div{
  padding: 10px 20px;
  min-height: 50px;
}
div:not([id="footer"]){
  background: pink;
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151128172336728)
### :empty
用来选择没有任何内容的元素，这里没有内容指的是一点内容都没有，**哪怕是一个空格都不行**。
e.g.
HTML：
```
<body>
    <div>我这里有内容</div>
    <div> </div><!-- 我这里有一个空格 -->
    <div></div><!-- 我这里任何内容都没有 -->
</body>
```
CSS:
```css
div {
  min-height: 30px;
}

div:empty {
 background-color: green;
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151128172704304)

### F :first-child
选择F的第一个子元素(所有第一个子元素都会被选择)
e.g. 这里作为F(selector1)的第一个子元素的有 span、div里的p、ul的第一个li
HTML:
```
<body>
	<div id="selector1">
		<span>我是第一个span</span>
		<p>我是第一个p，在span后面</p>
		<div><p>第二个p</p></div>
		<p>第三个p</p>
		<p>第四个p</p>
		<em>I am em</em>
		<p>iewnvk</p>
		<ul>
			<li>1</li>
			<li>2</li>
			<li>3</li>
			<li>4</li>
			<li>5</li>
		</ul>
	</div> 
</body>
```
CSS:
```css
#selector1 :first-child{
	color: pink;
}
```
效果：
 ![这里写图片描述](http://img.blog.csdn.net/20151128164505665)
注意：

 1. ：first-child的冒号要与前面的元素有一个空格的距离，否则它会把父元素里所有的子元素都选上
 
#### F E:first-child
针对F的第一个子元素**们**里的第一个E元素进行选择
e.g.
HTML代码如上例
CSS：
```css
#selector1 p:first-child{
	color: pink;
}
```
效果:
![这里写图片描述](http://img.blog.csdn.net/20151128164722358)
 
#### E:first-child
选择作为第一个子元素的E元素，对这个没有空格

重要：**之所以会有这么多的变化，个人认为冒号前面的空格其实是个派生选择器**
### :last-child
选择最后一个子元素
各种变化和:first-child一样，不再举例。
### :nth-child(n)
用来定位某父元素的一个或多个特定的子元素。其中“n”是参数，可以是整数值(1,2,3,4)，也可以是表达式(2n+1、-n+5)和关键词(odd、even)，**参数n的起始值始终是1**。也就是说，参数n的值为0时，选择器将选择不到任何匹配的元素。

 - tr:nth-child(2n+1):匹配奇数行的tr
 -  tr:nth-child(2n):匹配偶数行的tr
 -  tr:nth-child(odd):匹配奇数行的tr
 -  tr:nth-child(even):匹配偶数行的tr
 -  tr:nth-child(4):匹配第四行的tr
 
HTML：
```
<body>
    <ol>
      <li>item1</li>
      <li>item2</li>
      <li>item3</li>
      <li>item4</li>
      <li>item5</li>
      <li>item6</li>
      <li>item7</li>
      <li>item8</li>
      <li>item9</li>
      <li>item10</li>
    </ol>
</body>
```
CSS:
```css
ol > li:nth-child(2n-1){
  background: pink;
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151128173318195)
### :nth-last-child(n)
选择在其父元素中倒数第n个位置的元素或特定某元素。
计算顺序与:nth-child不同，其余用法相同，不再举例。
### :first-of-type
选择器类似于“:first-child”选择器，不同之处就是**指定了元素的类型**,其主要用来定位一个父元素下的某个类型的第一个子元素。
在E:first-of-type功能类似于E:nth-of-type(1)
e.g.
HTML:
```
<body>
    <div class="wrapper">
      <p>我是第一个段落</p>
      <p>我是第二个段落</p>
      <div>我是第一个Div元素</div>
      <div>我是第二个Div元素</div>
      <p>我是第三个段落</p>
      <p>我是第四个段落</p>
      <div>我是第三个Div元素</div>
      <div>我是第四个Div元素</div>
    </div>
</body>
```
CSS:
```css
.wrapper > p,
.wrapper > div {
  margin: 10px 0;
  background:#89c3eb;
  color: #fff;
  padding: 5px;
}

.wrapper > div:first-of-type {
  background: #928178;
}
```

效果：
![这里写图片描述](http://img.blog.csdn.net/20151128174206510)
### :last-of-type
选择器和“:first-of-type”选择器功能是一样的，不同的是他选择是父元素下的某个类型的最后一个子元素。不再举例
### :nth-of-type(n)
选择器和“:nth-child(n)”选择器非常类似，不同的是它**只计算父元素中指定的某种类型的子元素**[即E:nth-of-type(n)]。当某个元素中的子元素不单是同一种类型的子元素时，使用“:nth-of-type(n)”选择器来定位于父元素中某种类型的子元素是非常方便和有用的。在“:nth-of-type(n)”选择器中的“n”和“:nth-child(n)”选择器中的“n”参数也一样，可以是具体的整数，也可以是表达式，还可以是关键词。
**【所有匹配E的子元素被分离出来单独排序，不匹配的不参与排序】**
e.g.
HTML：
```
<body>
    <div class="wrapper">
      <div>我是一个Div元素</div>
      <p>我是一个段落元素</p>
      <div>我是一个Div元素</div>
      <p>我是一个段落</p>
      <div>我是一个Div元素</div>
      <p>我是一个段落</p>
      <div>我是一个Div元素</div>
      <p>我是一个段落</p>
      <div>我是一个Div元素</div>
      <p>我是一个段落</p>
      <div>我是一个Div元素</div>
      <p>我是一个段落</p>
      <div>我是一个Div元素</div>
      <p>我是一个段落</p>
      <div>我是一个Div元素</div>
      <p>我是一个段落</p>
    </div>
</body>
```
CSS:
```css
.wrapper > div:nth-of-type(odd),
.wrapper > p:nth-of-type(even){
  background: #59b9c6;
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151128174757084)
### :nth-last-of-type(n)
选择器和“:nth-of-type(n)”选择器是一样的，选择父元素中指定的某种子元素类型，但它的**起始方向是从最后一个子元素开始**，而且它的使用方法与“:nth-last-child(n)”选择器一样，不再举例。
### :only-child
匹配的元素的父元素中**有且仅有一个子元素**。【E:only-child】
选择的是这个E元素，也就是子元素。说不清楚就看例子：
e.g.
HTML：
```
<body>
    <ul>
      <li>Item1</li>
      <li>Item2</li>
      <li>Item3</li>
    </ul>
    <ul>
      <li>Item1</li>
    </ul>
    <ol>
      <li>Item1</li>
    </ol>
    <ol>
      <li>Item1</li>
      <li>Item2</li>
      <li>Item3</li>
    </ol>
</body>
```
CSS:
```css
li {
  background: #84b9cb;
  padding: 10px;
  margin-bottom: 5px;
}
li:only-child {
  background: #a99e93;
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151128182338963)
### only-of-type
选择其父元素只包含一个同类型的子元素
【表示一个元素有很多个子元素，而其中只有一种类型的子元素是**唯一的**，使用“:only-of-type”选择器就可以选中这个元素中的唯一一个类型子元素。】
还是不好表述，看例子
e.g.
HTML :
```
<body>
    <div class="wrapper">
      <p>我是一个段落</p>
      <p>我是一个段落</p>
      <p>我是一个段落</p>
    </div>
    
    <div class="wrapper">
      <p>我是一个段落</p>
    </div>
    
    <div class="wrapper">
      <div>我是一个Div元素</div>
      <p>我是一个段落</p>
      <div>我是一个Div元素</div>
    </div>
</body>
```
CSS:
```css
.wrapper {
  border: 1px solid #ccc;
  padding: 10px;
  width: 500px;
  margin: 10px auto;
}

.wrapper p:only-of-type{
  background: #84b9cb;
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151128182746127)
## 其他的常见伪类选择器
### :link和:visited
:link表示未访问的超链接，visite表示已访问的。
e.g.
HTML:
```
<body>
	<a href="#">我未访问</a>
	<a href="##">我已访问</a>
</body>
```
CSS:
```css
a:link{
	color: pink;
	font-size: 20px;
}
a:visited{
	color: #7b8d42;
	font-size: 20px;
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151128183354138)
### :hover
鼠标移动到容器，不仅限于链接，可用于页面中的任何元素（IE6中仅可用于链接）
e.g.
HTML：
```
<body> 
	<input type="text" />
</body>
```
CSS:
```css
input:hover{
	height: 100px;
 	background-color: #c099a0;
 }
```
效果:
鼠标未放上：
![这里写图片描述](http://img.blog.csdn.net/20151128183950550)
鼠标放上去：
![这里写图片描述](http://img.blog.csdn.net/20151128184004383)
### :active
被激活时的状态，不仅限于链接，可用于任何具有tabindex属性的元素，
**对a标签来说就是鼠标按下去的时候**
**在CSS中，a:hover必须置于a:link和a:visited后才生效，a:active必须置于a:hover后才生效**

e.g.
HTML
```html
<body> 
	<a href="#">我是链接</a>
</body>
```
CSS:
```css
a:active  {color:yellow;}
```
效果:
![这里写图片描述](http://img.blog.csdn.net/20151128184516338)
### :focus
用于设置获取焦点时的样式，不仅限于链接
e.g.
HTML：
```html
<body> 
	<input type="text" />
</body>
```
CSS :
```css
input:focus{
	background-color: #93ca76;
}
```
效果：
未获取焦点时：
![这里写图片描述](http://img.blog.csdn.net/20151128185032044)
获取焦点后：
![这里写图片描述](http://img.blog.csdn.net/20151128185044680)
### :enabled和:disabled
>在Web的表单中，有些表单元素有可用（“:enabled”）和不可用（“:disabled”）状态，比如输入框，密码框，复选框等。在默认情况之下，这些表单元素都处在可用状态。那么我们可以通过伪选择器“:enabled”对这些表单元素设置样式。
e.g.
HTML：
```html
<body>
    <form action="#">
      <div>
        <label for="enabled">可用输入框:</label>
        <input type="text" id="enabled" />
      </div>
      <div>
        <label for="disabled">禁用输入框:</label>
        <input type="text" id="disabled" disabled="disabled" />
      </div>
     </form>  
 </body>
```
CSS :
```css
div {
  margin: 30px;
}
input[type="text"]:enabled{
  border:3px solid #8491c3;
  box-shadow: 0 0 5px #8491c3;
}

input[type="text"]:disabled{
  box-shadow: none;
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151128185609350)
 
### :checked
在表单元素中，单选按钮和复选按钮都具有选中和未选中状态。在CSS3中可以通过状态选择器“:checked”配合其他标签实现自定义样式。而“:checked”表示的是选中状态。
e.g.
HTML：
```html
<form action="#">
  <div class="wrapper">
    <div class="box">
      <input type="radio" checked="checked"  id="boy" name="1" /><span></span>
    </div>
    <label for="boy">男</label>
  </div>
  
  <div class="wrapper">
    <div class="box">
      <input type="radio"  id="girl" name="1"  /><span></span>
    </div>
    <label for="girl">女</label>
  </div>
</form> 
```
CSS:
```css
form {
  border: 1px solid #ccc;
  padding: 20px;
  width: 300px;
  margin: 30px auto;
}
.wrapper {
  margin-bottom: 10px;
}
.box {
  display: inline-block;
  width: 30px;
  height: 30px;
  margin-right: 10px;
  position: relative;
  background: orange;
  vertical-align: middle;
  border-radius: 100%;
}
.box input {
  opacity: 0;
  position: absolute;
  top:0;
  left:0;
  width: 100%;
  height:100%;
  z-index:100;/*使input按钮在span的上一层，不加点击区域会出现不灵敏*/
}

.box span { 
  display: block;
  width: 10px;
  height: 10px;
  border-radius: 100%;
  position: absolute;
  background: #fff;
  top: 50%;
  left:50%;
  margin: -5px 0  0 -5px;
  z-index:1;
}

input[type="radio"] + span {
  opacity: 0;

}
input[type="radio"]:checked + span {
  opacity: 1;
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151128190337776)
### :read-only
用来指定处于只读状态元素的样式。
e.g.
HTML:
```html
<form action="#">
  <div>
    <label for="name">姓名:</label>
    <input type="text" name="name" id="name" placeholder="大漠" />
  </div>
  <div>
    <label for="address">地址:</label>
    <input type="text" name="address" id="address" placeholder="中国上海" readonly="readonly" />
  </div>
  <div>
    <label for="comment">评论：</label>
    <textarea name="comment" id="" cols="30" rows="10" readonly="readonly"></textarea>
  </div>
</form>
```
CSS:
```css
form {
  width: 300px;
  padding: 10px;
  border: 1px solid #ccc;
  margin: 50px auto;
}
form > div {
  margin-bottom: 10px;
}
input[type="text"]{
  border: 1px solid orange;
  padding: 5px;
  background: #fff;
  border-radius: 5px;
}
input[type="text"]:-moz-read-only{
  border-color: #ccc;
}
input[type="text"]:read-only{
  border-color: #ccc;
}
textarea:-moz-read-only{
  border: 1px solid #ccc;
  height: 50px;
  resize: none;
  background: #eee;
}
textarea:read-only {
  border: 1px solid #ccc;
  height: 50px;
  resize: none;
  background: #eee;
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151128190637704)

### :read-write
用来指定当元素处于非只读状态时的样式。
e.g.
HTML:
```html
<form action="#">
  <div>
    <label for="name">姓名:</label>
    <input type="text" name="name" id="name" placeholder="大漠" />
  </div>
  <div>
    <label for="address">地址:</label>
    <input type="text" name="address" id="address" placeholder="中国上海" readonly="readonly" />
  </div>
</form> 
```
CSS:
```css
form {
  width: 300px;
  padding: 10px;
  border: 1px solid #ccc;
  margin: 50px auto;
}
form > div {
  margin-bottom: 10px;
}
input[type="text"]{
  border: 1px solid orange;
  padding: 5px;
  background: #fff;
  border-radius: 5px;
}
input[type="text"]:-moz-read-only{
  border-color: #ccc;
}
input[type="text"]:read-only{
  border-color: #ccc;
}
input[type="text"]:-moz-read-write{
  border:2px solid red;
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151128190841504)
 



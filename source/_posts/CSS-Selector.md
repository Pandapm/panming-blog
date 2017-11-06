---
title: CSS选择器详解
date: 2015-11-22 21:18:46
tags: CSS
categories: Web前端 
---
选择器是CSS的核心，从最初的CSS版本到现在的CSS3，选择器的发展变化使得定位页面内的某个元素的方式越来越简单和多样化。
需要查找某个选择器讲解的直接在目录里选择，由于伪类比较多而且这篇略长，伪类将重新单独一篇~

<!-- more -->
## 元素选择器（W3C标准中又称类型选择器）
选择文档树中的元素。
e.g.
HTML:
```html
<body>
	<p>this is an example</p>
</body>
```
CSS:
```css
p{
	font-size: 50px;
	color: red;
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151122120300894)
## 派生选择器
依据元素在其位置的上下文关系来选择
### 后代选择器
在后代选择器中，样式左边的选择器一端包括两个或多个用空格隔开的选择器，他们之间的空格是结合符。
e.g.
HTML：
```html
<body>
	<ul>
		<li>
			<div>li的div</div>
		</li>
		<li>
			<div>li的div</div>
		</li>
		<li>
			<div><div>li的div的div</div></div>
		</li>
		<li><p>li的p</p></li>
		<li><a><div>li的a的div</div></a></li>
	</ul>
</body>
```
CSS:
```css
li div{
	font-size: 50px;
	color: red;
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151122134630949)

e.g.2
HTML如上
CSS：
```css
li a div{
	font-size: 50px;
	color: red;
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151122134808188)

**1、必须从左往右读选择器**
**2、后代选择器的意思是选择该元素所有的后代元素，该元素和后代元素之间的层次间隔可以是无限的。也就是说，只要是它的后代，无论隔了多少层，都会被选出来。**
### 子元素选择器
子元素选择器**只选择**某元素的子元素。
e.g.
HTML:
```html
<body>
	<ul>
		<li>
			<div>li的div</div>
		</li>
		<li>
			<div>li的div</div>
		</li>
		<li><p>li的p</p></li>
		<li><a><div>li的a的div</div></a></li>
	</ul>
</body>
```
CSS:
```css
li > div{
	font-size: 50px;
	color: red;
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151122173401959)
**其中的“>”是子结合符，两边可以有空格**
### 相邻兄弟选择器
选择紧邻在一个元素后的元素，他俩具有相同的父元素
e.g.
HTML：
```html
<body>
	<h1>我是h1</h1>
	<p>我是h1后面紧邻的p</p>
	<p>我和h1中间隔了一个p</p>
</body>
```
CSS:
```css
h1 + p{
	color: blue;
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151122174101274)
**其中的“+”是相邻兄弟结合符，两边可以有空格**
### 一般兄弟选择器
选择在某元素后的所有和它有着同一个父元素的元素
e.g.
HTML:
```html
<body>
	<h1>我是h1</h1>
	<p>我是h1后面紧邻的p</p>
	<p>我和h1中间隔了一个p</p>
	<div><p>我是div里的p</p></div>
	<p>我是最后一个p</p>
</body>
```
CSS:
```css
h1 ~ p{
	color: blue;
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151122174653408)
**其中的“~”是一般兄弟结合符**
## 类选择器
语法：

.类名{
	styles
}
或
*.类名{ styles }
**通配符*可以省略**
可以结合元素选择器使用
匹配class属性中包含指定值的**所有元素**
e.g.
HTML：
```html
<body>
	<h1 class="cls1">我是h1</h1>
	<p  class="cls1">我是h1后面紧邻的p</p>
	<p>我和h1中间隔了一个p</p>
	<div><p  class="cls1">我是div里的p</p></div>
	<p>我是最后一个p</p>
</body>
```
CSS :
```css
.cls1{
	color: blue;
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151122180151511)

 e.g.2
CSS：
```css
p.cls1{
	color: blue;
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151122180309260)
### 多类选择器
**仅**可以选择**同时**包含这些类名的元素，类名的顺序不变
e.g.
HTML：
```html
<body>
	<h1 class="cls2 cls1">我是h1</h1>
	<p  class="cls1">我是h1后面紧邻的p</p>
	<p>我和h1中间隔了一个p</p>
	<div><p  class="cls1 cls2">我是div里的p</p></div>
	<p>我是最后一个p</p>
</body>

```
CSS:
```css
.cls1.cls2{
	color: blue;
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151122180652703)

**重要：IE7之前版本的IE浏览器不能正确处理多类选择器**
## ID选择器
语法：
```css
#ID名{ styles }
或
*#ID名{ styles }  *通配符选择器可省略
```
e.g.
HTML：
```html
<body>
	<h1 >我是h1</h1>
	<p id="id1" >我有id1</p>
	<p>我和h1中间隔了一个p</p>
	<div><p>我是div里的p</p></div>
	<p>我是最后一个p</p>
</body>
```
CSS:
```css
#id1{
	color: blue;
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151122181321273)
类选择器与ID选择器的区别：

 - 一个ID在一个HTML文档中只能使用一次
 - ID选择器不能结合使用，因为ID的属性值中不允许有用空格分隔的词列表
 - ID可以包含更多的含义(如下)
 >类似于类，可以独立于元素来选择 ID。有些情况下，您知道文档中会出现某个特定 ID 值，但是并不知道它会出现在哪个元素上，所以您想声明独立的 ID 选择器。例如，您可能知道在一个给定的文档中会有一个 ID 值为 mostImportant 的元素。您不知道这个最重要的东西是一个段落、一个短语、一个列表项还是一个小节标题。您只知道每个文档都会有这么一个最重要的内容，它可能在任何元素中，而且只能出现一个。
 
 **类与ID选择器是否区分大小写取决于文档的语言，在HTML和XHTML中，ID和类被定义为*区分*大小写**

## 属性选择器
根据元素的属性及其属性值来选择元素
### 简单属性选择器
语法：
*[属性名] { 样式 }
e.g.
HTML：
```html
<body>
	<h1>我是h1</h1>
	<a href="#" title="tt1">我有title tt1</a>
	<p title="tt1">我也有title tt1</p>
</body>
```
CSS:
```css
[title]{
	color: blue;
	/*选择包含有title这个属性的所有元素*/
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151122182401198)
e.g.2
HTML：
```html
<body>
	<h1>我是h1</h1>
	<a href="#" title="tt1">我是a有href</a>
	<p title="tt1">我也有title tt1</p>
	<a>我是a没有href</a>
</body>
```
CSS:
```css
a[href]{
		color: blue;
}
/*选择有href的a元素*/
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151122182715121)
e.g.3
HTML：
```html
<body>
	<h1>我是h1</h1>
	<a href="#" title="tt1">我是a有href有title</a>
	<p title="tt1">我也有title tt1</p>
	<a href="">我是a有href没title</a>
</body>
```
CSS:
```css
a[href][title]{
	color: red;
}
/*选择同时有href和title属性的a元素*/
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151122183049001)
### 根据具体属性值选择
e.g.
HTML
```html
<body>
	<h1>我是h1</h1>
	<a href="#" title="tt1">我是a有href有title=tt1</a>
	<p title="tt1">我不是a,也有title tt1</p>
	<a >我是a没title</a>
</body>
```
CSS:
```css
a[title='tt1']{
	color: red;
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151122184108778)


**p.s.具体属性值这种格式要求属性值*必须完全匹配*才能选出来**
e.g.
HTML：
```html
<body>
	<p class="class1 class2">我的属性是 class="class1 class2"</p>
	<p class="class2 class1">我的属性是 class="class2 class1"</p>
	<p class="class1">我的属性是 class="class1"</p>
</body>
```
CSS:
```css
p[class='class1 class2']{
	color: pink;
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151122201527087)

### 根据多个属性值选择
也就是把多个属性-值选择器链接在一起使用
e.g.
HTML:
```html
<body>
	<a href="123" title="a1">我是a， href="123" title="a1"</a>
	<br/>
	<a href="1234" title="a1">我是a， href="1234" title="a1"</a>
</body>

```
CSS:
```css
a[href='123'][title='a1']{
	color: pink;
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151122201907079)
### 根据部分属性值选择
若需要根据属性值的词列表中的**某个词**（是完整的一个词）进行选择，则需使用“~”
e.g.
HTML ：
```html
<body>
	<p class="class1 class2">我的属性是 class="class1 class2"</p>
	<p class="class2 class1">我的属性是 class="class2 class1"</p>
	<p class="class1">我的属性是 class="class1"</p>
</body>
```
CSS:
```css
p[class~='class1']{
	color: pink;
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151122202859839)

子串匹配属性选择器

 - [attr^='value']：attr属性值以"value"开头的所有元素（必须是整个属性值列表里第一个词的开头）
 e.g.
 html： 
```
<body>
	<p class="class1 class2">我的属性是 class="class1 class2"</p>
	<p class="po class2 class1">我的属性是 class="class2 class1"</p>
	<p class="class1">我的属性是 class="class1"</p>
</body>
```
CSS:
```css
p[class^='cla']{
	color: pink;
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151122203902365)

 - [attr$='value']：attr属性值以"value"结尾的所有元素（必须是整个属性值列表里最后一个词的结尾）
 - [attr*='value']：attr属性值中包含子串"value"的所有元素
 
 ### 特定属性选择器
 常见的用途是匹配语言值，以方便为不同的语言的文字添加样式
 （以下示例中的语言值是瞎编的）
e.g.
 HTML：
 
```
 <body>
	<p lang="en-ut">我的lang="en-ut"</p>
	<p lang="en ut">我的lang="en ut"</p>
	<p lang="ent-ut">我的lang="ent-ut"</p>
	<p lang="en-fra">我的lang="en-fra"</p>
</body>
```
CSS:
```css
p[lang|='en']{
	color: pink;
}
/*选择带有以“en”开头的lang属性的元素*/
/*[attr|=value]  value必须是整个单词*/
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151122204532045)
##伪元素
语法：
>选择器:伪元素 { 样式 }

为了与伪类区分，常使用下面的形式：

>选择器::伪元素 { 样式 }
### ::first-line伪元素
用于向文本的首行设置样式
e.g.
HTML：
```
<body>
	<p>
		我是第一行<br/>
		我是第二行<br/>
		我是第三行<br/>
	</p>
</body>
```
CSS:
```css
p::first-line{
	color: pink;
}
```
效果：![这里写图片描述](http://img.blog.csdn.net/20151122205655787)
**可用于::first-line的属性：**
> 
>  - font
>  - color
>  - background
>  - word-spacing
>  - letter-spacing
>  - text-decoration
>  - vertical-align
>  - text-transform
>  - line-height
>  - clear

**::first-line伪元素只能用于块级元素**
### ::first-letter伪元素
用于向文本首字母添加样式
e.g.
HTML
``` 
<body>
	<p>
		我是第一行<br/>
		我是第二行<br/>
		我是第三行<br/>
	</p>
</body>
```
CSS:
```css
p::first-letter{
	color: pink;
	font-size: 30px;
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151122210401311)
**可用于::first-letter的属性：**

 

>  - font
>  -  color
>  - background
>  - margin
>  - padding
>  - border
>  - text-decoration
>  - vertical-align(仅当float为none时)
>  - text-transform
>  - line-height
>  - float
>  - clear

**只能用于块级元素**
### ::before伪元素
可使用content属性在元素的内容前插入新内容
e.g.
HTML:
```
<body>
	<p>
		我是第一行<br/>
		我是第二行<br/>
		我是第三行<br/>
	</p>
</body>
```
CSS:
```css
p::before{
	content: "|";
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151122211258795)
### ::after伪元素
用于在元素内容后插入新内容，使用方法与::before相同。
### ::selection伪元素
用来匹配突出显示的文本(用鼠标选择文本时的文本)。浏览器默认情况下，用鼠标选择网页文本是以“深蓝的背景，白色的字体”显示的。火狐浏览器需要加前缀
e.g.
HTML:
```
<body>
	<p>“::selection”伪元素是用来匹配突出显示的文本。浏览器默认情况下，选择网站文本是深蓝的背景，白色的字体，
有的设计要求不使用上图那种浏览器默认的突出文本效果，需要一个与众不同的效果，此时“::selection”伪元素就非常的实用。不过在Firefox浏览器还需要添加前缀。
	</p>
</body>
```
CSS:
```css
::selection{
  background: orange;
  color: white;
}
::-moz-selection{
  background: orange;
  color: white;
}
```
效果：
![这里写图片描述](http://img.blog.csdn.net/20151122211719305)



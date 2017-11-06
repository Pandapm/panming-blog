---
title: 浏览器渲染的过程
date: 2017-10-24 20:47:07
tags: [浏览器,优化]
categories: Web前端 
---
## 浏览器从请求到渲染页面的过程
浏览器从开始请求到渲染呈现一个页面的过程中时间花在下面五件事上：
 1. DNS查询
 2. 建立TCP连接
 3. 发出HTTP请求
 4. 等待服务器响应
 5. 渲染内容

<!-- more -->
而渲染页面内容这一步又可以继续细分成下面的步骤：
 1. 处理 HTML，构建 DOM 树（文档对象模型）
 2. 处理 CSS，构建构建 CSSOM（CSS对象模型）
 3. 以 DOM 和 CSSOM 为基础，构建渲染树。渲染树反应了除不可见元素（ `<head>`,`display:none` ）之外的 DOM 结构中一切可见元素，每一段字符串在渲染树中都被当做独立的渲染对象，每一个渲染对象都是其对应的 DOM 结构和计算所得样式的混合体（即： 渲染树是 DOM 树的视觉表现）
 4. 根据渲染树计算元素的坐标，进行布局（layout）
 5. 将节点绘制到屏幕上（painting）

当 DOM 或 CSSOM 被修改时，以上步骤会重复执行，比如重绘（repaint, 元素样式发生变化，但并不影响元素的位置和周围元素）和重排（reflow, 元素的改变影响了文档内容或结构，或影响了元素的位置，重排会包含重绘）。

## 关键渲染路径（Critical Rendering Path）
关键渲染路径可以理解为浏览器从加载HTML文档到完成首屏渲染的过程中必须要经过的步骤，具体就是浏览器收到 HTML、CSS 和 JavaScript 等资源并对其进行处理从而渲染出 Web 页面，关键路径的长度指获取所有阻塞资源（关键资源）所需的往返次数， 比如说样式文件，脚本文件。图片不属于关键资源，因为图片不会导致阻塞游览器渲染。
关键渲染路径决定了首屏渲染需要的时间，了解浏览器渲染的过程很大程度上是为了优化关键渲染路径。不过大多数时候讨论关键路径都是在讨论 CSS 和 JS，因为它们可能会阻塞渲染。
![渲染路径](http://img.blog.csdn.net/20171025142257519?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUGFuZGFfbQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
*图 1  关键路径*
			
![这里写图片描述](http://img.blog.csdn.net/20171025143345240?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUGFuZGFfbQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
*图 2  DOMContentLoaded与onLoad*

蓝色表示触发 DOMContentLoaded 事件的时间点，红色表示触发 onload 事件的时间点。箭头附近的蓝色色块是解析 HTML（Parse HTML）的时间，黄色是执行 js（Evaluate Script）的时间，紫色是布局（Layout）的时间。大多数的 JavaScript 框架的逻辑开始部分都是从 DOMContentLoaded 事件点开始的，因为 DOMContentLoaded 总比 onload 快， onload 会等到图片等资源都就绪才触发，会拖慢首屏时间。从图片可以看出，在DOMContentLoaded之后的时间里已经在开始对首屏进行渲染了，也就意味着把DOMContentLoaded提前可以让用户感觉这个页面打开的稍微能快一点。

![这里写图片描述](http://img.blog.csdn.net/20171025144605579?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUGFuZGFfbQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
*图 3    vue的逻辑部分从DOMContentLoaded事件点开始*
图3中被选中的黄色框是执行vue打成的一个包的时间
## 阻塞渲染
目前浏览器总是并行加载资源，例如，当 HTML 解析器被脚本阻塞时，解析器虽然会停止构建 DOM，但仍会识别该脚本后面的资源，并进行预加载。
- 默认情况下，CSS 会被视为祖册渲染的资源，也就意味着在 CSSOM 构建完成前浏览器将不渲染其他已处理的内容
- 存在阻塞的 CSS 资源时，浏览器会延迟 JavaScript 的执行和 DOM 的构建
- CSSOM 构建时，JavaScript 暂停执行
- 浏览器遇到 `<script>` 标签时，DOM 构建将暂停
- JavaScript 可读取、更改 DOM 和 CSSOM

综上，实际使用的时候在引用顺序上会让 CSS 资源优先于 JavaScript 资源引入。
尽早在 HTML 文档内指定所有 CSS 资源，以便浏览器尽早发现 link 标记并尽早发出 CSS 请求

## 解除 CSS 和 JavaScript 对渲染的阻塞
### CSS
```html
<style> p { color: red; }</style>
<link rel="stylesheet" href="index.css">
```
这样的 `<style>` 和 `<link>` 标签会被视为阻塞渲染的资源，浏览器辉优先处理这些 CSS。
在关键渲染路径中，要求同时具有 DOM 和 CSSOM 后才会构建渲染树（画外音： HTML也是阻塞渲染的资源）。除了精简 CSS 之外，可以用媒体类型和媒体查询来解除 CSS 对渲染的阻塞：
```html
<link href="index.css" rel="stylesheet">
<link href="print.css" rel="stylesheet" media="print">
<link href="other.css" rel="stylesheet" media="(min-width: 30em) and (orientation: landscape)">
```
媒体类型允许指定的样式表如何在不同媒体呈现。该文件可以以不同的方式显示在屏幕上，在纸张上，或听觉浏览器上，而且一些CSS属性只设计了某些媒体。例如 `voice-family` 属性是专为听觉用户代理。例如，在浏览器屏幕上显示一个 14 像素的 `Verdana` 字体；但页面打印出来是 10 像素的 `Times` 字体：
```css
@media screen
{
    p {
        font-family: verdana,sans-serif;
        font-size: 14px;
    }
}
@media print
{
    p {
        font-family: times,serif;
        font-size: 10px;
    }
}
```
第一个资源会加载并阻塞。
第二个资源设置了媒体类型，会加载但不会阻塞，`print` 只在打印网页时使用。
第三个资源提供了媒体查询，会在符合条件时阻塞渲染。

### JavaScript
比如这样的两个情况：
```html
<p>11111</p>
<script>console.log("inline")</script>
<p>22222</p>
<script src="app.js"></script>
<p>33333</p>
 
<p>11111</p>
<script src="app.js"></script>
<p>22222</p>
<script>console.log("inline")</script>
<p>33333</p>
```
两种情况下解析 HTML（p标签）的过程都会被打断两次，所以实际应用中 JS 资源通常被放在文档底部。
`<script>` 标签有两个属性可以改变它的阻塞模式： `defer` 和 `async`，这两个属性对于像 `<script>console.log("inline")</script>` 这样的inline-script是无效的。
#### defer
defer 属性表示延迟执行引入的 JavaScript，即这段 JavaScript 加载时 HTML 并未停止解析，这两个过程是并行的。整个 document 解析完毕且 defer-script 也加载完成之后（这两件事情的顺序无关），会执行所有由 defer-script 加载的 JavaScript 代码，然后触发 `DOMContentLoaded` 事件。
defer 不会改变 script 中代码的执行顺序，代码会 `<script>` 标签加载的顺序执行。所以，defer 与相比普通 script 区别是：载入 JavaScript 文件时不阻塞 HTML 的解析，执行阶段被放到 HTML 标签解析完成之后。

#### async
`async` 属性表示异步执行引入的 JavaScript，与 `defer` 的区别在于，如果已经加载好，就会开始执行——无论此刻是 HTML 解析阶段还是 `DOMContentLoaded` 触发之后。需要注意的是，这种方式加载的 JavaScript 依然会阻塞 `load` 事件。换句话说，async-script 可能在 `DOMContentLoaded` 触发之前或之后执行，但一定在 load 触发之前执行。
多个 async-script 的执行顺序是不确定的。需要注意的是，向 document 动态添加 `<script>` 标签时，async 属性默认是 `true`。

#### document.createElement
上面 async 这段提到了向 document 中动态添加 `<script>` 标签时它的 `async` 属性默认为 `true`，所以也就不会阻塞页面了。想同步执行的话需要手动把 `async` 设置成 `false`。


## 附录 媒体类型
媒体类型 | 描述 |
------ | ---------- |
all	| 所有的媒体设备 |
aural |语音和音频合成器 |
braille	| 盲人用点字法触觉回馈设备 |
embossed | 分页的盲人用点字法打印机 |
handheld | 小的手持的设备 |
print | 打印机 |
projection | 比如幻灯片 |
screen | 电脑/手机显示器 |
tty	| 使用固定密度字母栅格的媒体，比如电传打字机和终端 |
tv | 电视机类型的设备 |
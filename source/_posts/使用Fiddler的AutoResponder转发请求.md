---
title: 使用Fiddler的AutoResponder转发请求
date: 2017-09-08 11:11:36
tags: [fiddler,http proxy]
categories: Web前端 
---
前端同学进行开发的时候，经常需要调用远程的API请求数据，但囿于浏览器的同源策略不能直接发送跨域的请求，通常来说解决方案有：
- 添加一层nodejs进行接口转发
- 使用webpack的dev-server代理一下

在使用了webpack的项目里用dev-server还是很方便的。如果没有使用webpack也不想多写一层node的话，可以使用fiddler的AutoResponder转发请求。

<!-- more -->
![这里写图片描述](http://img.blog.csdn.net/20170908104530292?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUGFuZGFfbQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

选中Enable rules和Unmatched request passthrough，激活AutoResponder
![这里写图片描述](http://img.blog.csdn.net/20170908104742184?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUGFuZGFfbQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
根据选项提示看出，左边一栏里是请求的URL，右边是响应的URL，点击Add Rule可以在底部添加规则。

### 一个例子

首先简单粗暴上一个例子，如果我想要请求http://localhost:8080/api时获得请求http://xxxx.xx/api的效果，可以这么写：
 
 if request matches：REGEX:http://localhost:8080/api(?<argvs>.*)$
then respond with: http://xxxx.xx/api${argvs}
就可以了。

### 匹配规则

- 匹配时规则列表中出现的顺序进行匹配， 按+键可以使规则向上移一项，按-键可以使规则向下移一项
- 键菜单可以导出一个包含了所有的匹配规则和对应响应字符串的后缀为 .FARX 的文件
- 可以通过导入  .SAZ or .FARX 文件来创建基于以前创建的规则列表
- 可以从会话列表中拖动一个会话到AutoResponder选项卡中来重新演示以前的响应.
- 也可以直接从Windows的资源管理器中拖动一个文件到 AutoResponder选项卡中来为这些文件创建规则

规则写的时候使用不同的前缀代表不同的含义，常用的前缀有`REGEX`, `EXACT`, `NOT`和不使用前缀。 

- REGEX： 使用正则表达式进行匹配(**简直万能**)
- EXACT： 匹配时区分字母的大小写
- NOT： 待匹配的字符串中不包含当前的匹配串
- 不使用前缀： 匹配时不区分字母大小写

写好了匹配规则后点右边的`test`可以进行测试，如图
![这里写图片描述](http://img.blog.csdn.net/20170908110759790?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUGFuZGFfbQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
在Test URL中可以随便写字符串进行测试，蓝绿色表示匹配，红色表示不匹配。确定了匹配规则后点击右边的save使规则生效。

[fiddler官方文档](http://docs.telerik.com/fiddler/configure-fiddler/tasks/configurefiddler)
---
title: 聊聊Service-Worker
date: 2017-09-07 15:15:40
tags: JavaScript
categories: Web前端 
---
浏览器一般有三类 web Worker： 
- Worker：专用的 worker，只能被创建它的 JS 访问，生命周期到创建它的页面关闭时结束。 
- SharedWorker：共享的 worker，可以被好几个 JS 访问，生命周期到关联的页面都关闭时结束。 
ServiceWorker：一个特殊的 worker，生命周期与页面无关，关联页面未关闭时，它也可以退出，没有关联页面时，它也可以启动

<!-- more -->
## Web Workers
web worker: 为Web内容在后台线程中运行脚本提供了一种简单的方法，线程可以执行任务而不干扰用户界面,即：运行在后台的 JavaScript

浏览器一般有三类 web Worker：
- Worker：专用的 worker，只能被创建它的 JS 访问，生命周期到创建它的页面关闭时结束。
- SharedWorker：共享的 worker，可以被好几个 JS 访问，生命周期到关联的页面都关闭时结束。
ServiceWorker：一个特殊的 worker，生命周期与页面无关，关联页面未关闭时，它也可以退出，没有关联页面时，它也可以启动，嗯总之就是一个神奇的worker

对于workers来说，它运行的上下文不同于当前的window对象所在的上下文，在专用worker的情况下，DedicatedWorkerGlobalScope 对象代表了worker的上下文；在共享worker的情况下，SharedWorkerGlobalScope对象代表了共享worker的上下文。

因此，在worker线程中运行的代码是有一些特殊情况的，比如，不能直接操作DOM以及使用window对象的一些默认属性和方法。但是在worker里，还是有很多window对象之下的东西是可以使用的，具体就要查阅文档了 https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Functions_and_classes_available_to_workers

worker线程和主线程各自都使用postMessage()发送消息和和onmessage事件来响应对方发送的消息，传递的信息包含在 Message 这个事件的data属性内里，数据传递的是副本。

当然一个 worker 可以生成另外的新的 worker，这些 worker 的宿主和它们父页面的宿主相同。


## Service Worker

前两种worker主要是为了解决js执行耗时操作时影响UI响应的问题，而之所以说service worker是一种特殊的worker，是因为它想要把一个web APP变得更像native APP，可以支持离线访问。在service worker之前，离线缓存使用AppCache来做，从Firefox44起，当使用 AppCache 来提供离线页面支持时，会提示建议开发者使用 service workers 来实现离线页面。

- 它是一种 JavaScript 工作线程，无法直接访问 DOM。 service worker通过响应 postMessage 接口发送的消息来与其控制的页面通信，页面可在必要时对 DOM 执行操作。
- 它是一种可编程网络代理，能够控制并处理页面所发送的网络请求。
- 它在不用时会被中止，并在下次有需要时重启，因此不能依赖于service worker的 
`onfetch事件` 和 `onmessage事件` 处理程序中的全局状态。如果存在需要持续保存并在重启后加以重用的信息，service worker可以访问 IndexedDB API以及FireFox OS专用的Data Store API等数据存储机制。
- service worker 广泛利用了 promise
- service Workers 要求要在必须在 HTTPS 下才能运行,为了便于本地开发，localhost 也被浏览器认为是安全源。


### 生命周期

Service worker的生命周期完全独立于网页

1. 如果要使用service worker，首先要在js中进行注册，注册的动作会让浏览器在后台启动service worker的安装步骤
2. service worker获取的第一个事件为 install。该事件在工作线程执行时立即触发，并且只能被每个service worker调用一次。 如果更改service worker的代码，则浏览器将其视为一个不同的service worker，并且它将获得自己的 install 事件。在安装的过程中，如果所有需要离线缓存的静态资源都已经成功缓存，那么service worker就安装完成进入激活步骤，如果有文件下载失败或缓存失败，service worker就无法完成安装过程。
3. 安装之后进入激活步骤，可以对旧的缓存进行管理
4. 激活之后，service worker开始施展身手，对它作用域内的所有页面进行控制，首次注册service worker的页面需要再次加载时才会受控制。激活之后，service worker将处于以下两种状态之一：终止或处理onfetch和onmessage事件，从页面发出网络请求或消息后将会出现后一种状态。

如果 service worker 脚本版本处于 ACTIVATED 状态，功能事件处理完之后，service worker 线程会被终止，当再次有功能事件时，service worker 线程又会被启动，启动完成后 service worker 就可以立即进入 ACTIVATED 状态。

![生命周期](https://developers.google.cn/web/fundamentals/getting-started/primers/imgs/sw-lifecycle.png?hl=zh-cn)

浏览器内核会管理三种 service worker 脚本版本：

- installing_version：处于 INSTALLING 状态的版本
- waiting_version：处于 INSTALLED 状态的版本
- active_version：处于 ACTIVATED 状态的版本

installing_version 一般是在 service worker 线程启动后的版本，这是一个中间版本，在正确安装完成后会转入 waiting_version。

waiting_version 一般在注册信息已被存储的版本状态，或者在再次打开 service worker 页面时，检查到 service worker 脚本版本的状态为 INSTALLED，也会进入此版本状态。waiting_version 的存在确保了当前 scope 下只有一个生效的 service worker。

active_version 一般在 activate 事件处理完成后，就会处于此版本状态，同一 scope 下只有一个 active Service Worker。需要特别注意的是，当前页面已有 active worker 控制，刷新页面时，新版本 Waiting(Installed) 状态的 service worker 并不能转入 active 状态。

Service worker 可以从 waiting_version 转入 active_version 的条件:

- 当前 scope 下没有 active service worker 在运行。
- 页面 JS 调用 self.skipWaiting 跳过 waiting 状态。
- 用户关闭页面，释放了当前处于 active 状态的 service worker。
- 浏览器周期性检测，发现 active service worker 处于 idle 状态，就会释放当前处于 active 状态的 service worker。

其中，INSTALLED和ACTIVATED是稳定状态，可以对缓存之类的资源进行管理。

### E.G.

#### 1. 注册
service worker需要在页面中进行注册才能启动安装步骤，注册时需要告诉在service worker中执行的代码的地址

```javascript
if ('serviceWorker' in navigator) {
    window.addEventListener('load', function() {
        navigator.serviceWorker.register('/sw.js').then(function(registration) {
            // Registration was successful
            console.log('ServiceWorker registration successful with scope: ', registration.scope);
        }).catch(function(err) {
            // registration failed
            console.log('ServiceWorker registration failed: ', err);
        });
    });
}
```

此代码用于检查 Service Worker API 是否可用，如果可用，则在页面加载后注册位于 /sw.js 的service worker。
每次页面加载无误时，即可调用 register()；浏览器将会判断service worker是否已注册并做出相应的处理。

register() 方法的精妙之处在于service worker文件的位置。 本例中service worker文件位于根目录。 这意味着service worker的作用域将是整个来源。 换句话说，service worker可以接收此网域上所有的 fetch 事件。 如果在 /example/sw.js 处注册service worker文件，则service worker将只能看到URL以 /example/ 开头（即 /example/page1/、/example/page2/）的页面的 fetch 事件。

chrome://inspect/#service-workers 可以看到目前运行中的[service worker](chrome://inspect/#service-workers)

[这是Google的一个例子](https://cdn.rawgit.com/jakearchibald/80368b84ac1ae8e229fc90b3fe826301/raw/ad55049bee9b11d47f1f7d19a73bf3306d156f43/)
https://cdn.rawgit.com/jakearchibald/80368b84ac1ae8e229fc90b3fe826301/raw/ad55049bee9b11d47f1f7d19a73bf3306d156f43/

#### 2. 安装

启动注册后，需要为安装事件定义一个回调，决定哪些资源需要被缓存，在回调的内部，需要执行以下步骤：
1. 打开缓存
2. 缓存资源文件
3. 确定所有需要缓存的资源是否已经缓存完毕

在 install 事件中也可以执行其他任务，甚至不设置 install 事件侦听器也可以。

```javascript
var CACHE_NAME = 'my-site-cache-v1';
var urlsToCache = [
  '/',
  '/styles/main.css',
  '/script/main.js'
];

self.addEventListener('install', function(event) {
  // Perform install steps
  event.waitUntil(
    caches.open(CACHE_NAME)
      .then(function(cache) {
        console.log('Opened cache');
        return cache.addAll(urlsToCache);
      })
  );
});
```

### 3.开始工作
刷新当前页面后，service worker将开始接收 fetch 事件

```javascript
self.addEventListener('fetch', function(event) {
  event.respondWith(
    caches.match(event.request)
      .then(function(response) {
        // Cache hit - return response
        if (response) {
          return response;
        }
        return fetch(event.request);
      }
    )
  );
});
```

这里定义了 fetch 事件，并且在 event.respondWith() 中传入来自 caches.match() 的一个 promise。 此方法会检车这个请求，并从service worker创建的缓存中查找缓存的结果。

如果发现有匹配的结果，则返回命中的值，否则调用 fetch 发出网络请求，并将从服务器请求到的数据作为结果返回。

### 4. 更新service worker

1. 更新您的service worker JavaScript 文件。用户访问更新了之后的站点时，浏览器会尝试在后台重新下载service worker的脚本文件。如果service worker文件与其当前所用文件存在字节差异，则将其视为“新service worker”。
2. 新service worker将会启动，且触发 install 事件。
3. 此时，旧service worker仍控制着当前页面，新service worker将进入 waiting 状态。
4. 当网站上当前打开的页面关闭时，旧service worker将会被终止，新service worker将会取得控制权。
5. 新service worker取得控制权后，触发其 activate 事件。(旧service worker退出时将触发 新service worker的Activate，新service worker将能够控制客户端。 可以执行在仍使用旧工作线程时无法执行的操作，如迁移数据库和清除缓存。)
出现在 activate 回调中的一个常见任务是缓存管理，但缓存管理通常不在这个新service worker的install回调里面做，因为它installed的时候旧的service worker还在工作中。

比如说我们有一个名为 'my-site-cache-v1' 的缓存，我们想要将该缓存拆分为一个页面缓存和一个博文缓存。这就意味着在安装步骤中我们创建了两个缓存：'pages-cache-v1' 和 'blog-posts-cache-v1'，且在激活步骤中我们要删除旧的 'my-site-cache-v1'。

具体做法为：遍历service worker中的所有缓存，并删除未在缓存白名单中定义的任何缓存。

```javascript
self.addEventListener('activate', function(event) {

  var cacheWhitelist = ['pages-cache-v1', 'blog-posts-cache-v1'];

  event.waitUntil(
    caches.keys().then(function(cacheNames) {
      return Promise.all(
        cacheNames.map(function(cacheName) {
          if (cacheWhitelist.indexOf(cacheName) === -1) {
            return caches.delete(cacheName);
          }
        })
      );
    })
  );
});
```

等待阶段表示每次只能运行一个版本的service worker，但可以通过调用 self.skipWaiting() 用新的service worker把旧的那个逐出去，并在进入等待阶段时尽快激活自己（或立即激活，前提是已经处于等待阶段）。
skipWaiting() 在等待期间调用还是在之前调用并没有什么不同。 一般情况下是在 install 事件中调用它：

```javascript
self.addEventListener('install', event => {
  self.skipWaiting();

  event.waitUntil(
    // caching etc
  );
});
```

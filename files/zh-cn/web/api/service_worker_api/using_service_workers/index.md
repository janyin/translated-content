---
title: 使用 Service Workers
slug: Web/API/Service_Worker_API/Using_Service_Workers
---
{{ServiceWorkerSidebar}}

本文是关于使用 service workers 的教程，包括讲解 service worker 的基本架构、怎么注册一个 service worker、一个新 service worker 的 install 及 activation 过程、怎么更新 service worker 还有它的缓存控制和自定义响应，这一切都在一个简单的离线的应用程序中。

## 背景

有一个困扰 web 用户多年的难题——丢失网络连接。即使是世界上最好的 web app，如果下载不了它，也是非常糟糕的体验。如今虽然已经有很多种技术去尝试着解决这一问题。而随着[离线](/zh-CN/Apps/Build/Offline)页面的出现，一些问题已经得到了解决。但是，最重要的问题是，仍然没有一个好的统筹机制对资源缓存和自定义的网络请求进行控制。

之前的尝试 — AppCache — 看起来是个不错的方法，因为它可以很容易地指定需要离线缓存的资源。但是，它假定你使用时会遵循诸多规则，如果你不严格遵循这些规则，它会把你的 APP 搞得一团糟。关于 APPCache 的更多详情，请看 Jake Archibald 的文章： [Application Cache is a Douchebag](http://alistapart.com/article/application-cache-is-a-douchebag).

> **备注：** 从 Firefox44 起，当使用 [AppCache](/zh-CN/docs/Web/HTML/Using_the_application_cache) 来提供离线页面支持时，会提示一个警告消息，来建议开发者使用 [Service workers](/zh-CN/docs/Web/API/Service_Worker_API/Using_Service_Workers) 来实现离线页面。({{bug("1204581")}}.)

Service worker 最终要去解决这些问题。虽然 Service Worker 的语法比 AppCache 更加复杂，但是你可以使用 JavaScript 更加精细地控制 AppCache 的静默行为。有了它，你可以解决目前离线应用的问题，同时也可以做更多的事。 Service Worker 可以使你的应用先访问本地缓存资源，所以在离线状态时，在没有通过网络接收到更多的数据前，仍可以提供基本的功能（一般称之为 [Offline First](http://offlinefirst.org/)）。这是原生 APP 本来就支持的功能，这也是相比于 web app，原生 app 更受青睐的主要原因。

## 使用前的设置

在已经支持 serivce workers 的浏览器的版本中，很多特性没有默认开启。如果你发现示例代码在当前版本的浏览器中怎么样都无法正常运行，你可能需要开启一下浏览器的相关配置：

- **Firefox Nightly**: 访问 `about:config` 并设置 `dom.serviceWorkers.enabled` 的值为 true; 重启浏览器；
- **Chrome Canary**: 访问 `chrome://flags` 并开启 `experimental-web-platform-features`; 重启浏览器 (注意：有些特性在 Chrome 中没有默认开放支持)；
- **Opera**: 访问 `opera://flags` 并开启 `ServiceWorker 的支持`; 重启浏览器。

另外，你需要通过 HTTPS 来访问你的页面 — 出于安全原因，Service Workers 要求必须在 HTTPS 下才能运行。Github 是个用来测试的好地方，因为它就支持 HTTPS。为了便于本地开发，`localhost` 也被浏览器认为是安全源。

## 基本架构

通常遵循以下基本步骤来使用 service workers：

1. service worker URL 通过 {{domxref("serviceWorkerContainer.register()")}} 来获取和注册。
2. 如果注册成功，service worker 就在 {{domxref("ServiceWorkerGlobalScope") }} 环境中运行； 这是一个特殊类型的 worker 上下文运行环境，与主运行线程（执行脚本）相独立，同时也没有访问 DOM 的能力。
3. service worker 现在可以处理事件了。
4. 受 service worker 控制的页面打开后会尝试去安装 service worker。最先发送给 service worker 的事件是安装事件 (在这个事件里可以开始进行填充 IndexDB 和缓存站点资源)。这个流程同原生 APP 或者 Firefox OS APP 是一样的 — 让所有资源可离线访问。
5. 当 `oninstall` 事件的处理程序执行完毕后，可以认为 service worker 安装完成了。
6. 下一步是激活。当 service worker 安装完成后，会接收到一个激活事件 (activate event)。 `onactivate` 主要用途是清理先前版本的 service worker 脚本中使用的资源。
7. Service Worker 现在可以控制页面了，但仅是在 `register()` 成功后的打开的页面。也就是说，页面起始于有没有 service worker ，且在页面的接下来生命周期内维持这个状态。所以，页面不得不重新加载以让 service worker 获得完全的控制。

![](sw-lifecycle.png)

下图展示了 service worker 所有支持的事件：

![install, activate, message, fetch, sync, push](sw-events.png)

### Promises

[Promises](/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise) 是一种非常适用于异步操作的机制，一个操作依赖于另一个操作的成功执行。这是 service worker 的核心工作机制。

Promises 可以做很多事情。但现在，你只需要知道，如果有什么返回了一个 promise，你可以在后面加上 `.then()` 来传入成功和失败的回调函数。或者，你可以在后面加上 `.catch()` 如果你想添加一个操作失败的回调函数。

接下来，让我们对比一下传统的同步回调结构，和异步 promise 结构，两者在功能上是等效的：

#### 同步

```js
try {
  var value = myFunction();
  console.log(value);
} catch(err) {
  console.log(err);
}
```

#### 异步

```js
myFunction().then(function(value) {
  console.log(value);
}).catch(function(err) {
  console.log(err);
});
```

在上面第一个例子中，我们必须等待 myFunction() 执行完成，并返回 value 值，在此之前，后续其它的代码无法执行。在第二个例子中，myFunction() 返回一个 promise 对象，下面的代码可以继续执行。当 promise 成功 resolves 后，then() 中的函数会异步地执行。

现在来举下实际的例子 — 如果我们想动态地加载图片，而且要在图片下载完成后再展示到页面上，要怎么实现呢？这是一个比较常见的场景，但是实现起来会有点麻烦。我们可以使用 .onload 事件处理程序，来实现图片的加载完成后再展示。但是如果图片的 onload 事件发生在我们监听这个事件之前呢？我们可以使用 .complete 来解决这个问题，但是仍然不够简洁，如果是多个图片该怎么处理呢？并且，这种方法仍然是同步的操作，会阻塞主线程。

相比于以上方法，我们可以使用 promise 来实现。(可以看我们的 [Promises test](https://github.com/mdn/promises-test) 示例源码， [look at it running live](https://mdn.github.io/promises-test/).)

> **备注：** service worker 在实际使用中，会使用 caching 和 onfetch 等异步操作，而不是使用老旧的 XMLHttpRequest API。这里的例子使用 XMLHttpRequest API 只是为了让你能将注意力集中于理解 Promise 上。

```js
function imgLoad(url) {
  return new Promise(function(resolve, reject) {
    var request = new XMLHttpRequest();
    request.open('GET', url);
    request.responseType = 'blob';

    request.onload = function() {
      if (request.status == 200) {
        resolve(request.response);
      } else {
        reject(Error('Image didn\'t load successfully; error code:' + request.statusText));
      }
    };

    request.onerror = function() {
      reject(Error('There was a network error.'));
    };

    request.send();
  });
}
```

我们使用 Promise( ) 构造函数返回了一个新的 promise 对象，构造函数接收一个回调函数作为参数。这个回调函数包含两个参数，第一个为成功执行 (resolve) 的回调函数，第二个为执行失败 (reject) 的回调函数。我们将这两个回调函数在对应的时机执行。在这个例子中，resolve 会在请求返回状态码 200 的时候执行，reject 会在请求返回码为非 200 的时候执行。上面代码的其余部分基本都是 XHR 的相关操作，现在不需要过多关注。

当我们调用 imgLoad( ) 函数时，传入要加载的图片 url 作为参数。然后，后面的代码与同步方式会有点不同：

```js
var body = document.querySelector('body');
var myImage = new Image();

imgLoad('myLittleVader.jpg').then(function(response) {
  var imageURL = window.URL.createObjectURL(response);
  myImage.src = imageURL;
  body.appendChild(myImage);
}, function(Error) {
  console.log(Error);
});
```

在函数调用后面，我们串联了 promise 的 then() 方法。then() 接受两个函数 —— 第一个函数在 promise 成功执行的情况下执行，而第二个函数则在 promise 执行失败情况下执行。当执行成功时，在 myImage 中显示图片，并追加到 body 里面 (它的参数就是传递给 promise 的 resolve 方法的 request.response )；当执行失败时，在控制台返回一个错误。

这些都是异步的。

> **备注：** 你可以链式调用 promise，比如：
> `myPromise().then(success, failure).then(success).catch(failure);`

> **备注：** 你可以阅读 Jake Archibald 的精彩的文章 [JavaScript Promises: there and back again](http://www.html5rocks.com/en/tutorials/es6/promises/) 了解更多关于 promise 的内容

## Service workers demo

为了演示 service worker 的基本的注册和安装，我们做了一个简单的例子 [sw-test](https://github.com/mdn/sw-test)，这是一个简单的 Star wars Lego 图片库。采用了基于 promise 的函数从一个 JSON 对象来读取图片内容，在显示图片到页面上之前，采用 Ajax 来加载图片。页面非常简单，而且是静态的，但也注册、安装和激活了 service worker，当浏览器支持的时候，它将缓存所有依赖的文件，它可以在离线的时候访问！
![](demo-screenshot.png)

你可以查看 [Github 上的源码](https://github.com/mdn/sw-test/)， 也可以查看 [在线示例](https://mdn.github.io/sw-test/)。有一点需要我们重点关注的是 promise（查看 [app.js 22-47 行](https://github.com/mdn/sw-test/blob/gh-pages/app.js#L22-L47)），这是一个你上面读到的 [Promises test demo](https://github.com/mdn/promises-test) 里的一个修改版，它们有以下不同：

1. 原始的版本里，我们只传了一个我们想加载的图片的 URL 。在这个版本里，我们传了一个包含单个图片所有数据的 JSON（查看 [image-list.js](https://github.com/mdn/sw-test/blob/gh-pages/image-list.js)）。这是因为每一个 promise reslove 的所有数据必须传给 promise，因为它是异步的。如果你只传了 url ，那么当你 `for` 循环被遍历的时候你试图分别访问其他项，将不会有效的，因为 promise 的 resolve 不会和遍历（这个是同步的过程）同时完成。
2. 我们实际上用数组 resolve 了这些 promise，因为我们想让得到加载完的图片 blob 和 图片的名字、credit 和 alt 文本（查看 [app.js 31-34 行](https://github.com/mdn/sw-test/blob/gh-pages/app.js#L31-L34)）。Promises 只能 resolve 单个参数，所以你想 resolve 多个值的话，你需要用数组或对象。
3. 为了访问 promise resolved 的值，我们接着通过 then 函数进行获取（[app.js 60-64 行](https://github.com/mdn/sw-test/blob/gh-pages/app.js#L60-L64)），这个有点古怪，但这就是 promise 工作的方式。

## 现在来谈谈 Service workers

现在我们开始讨论 service workers ！

### 注册你的 worker

我们 app 的 JavaScript 文件里 — `app.js` — 的第一块代码就像下面的一样。这是我们使用 service worker 的入口：

```js
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('/sw-test/sw.js', { scope: '/sw-test/' }).then(function(reg) {
    // registration worked
    console.log('Registration succeeded. Scope is ' + reg.scope);
  }).catch(function(error) {
    // registration failed
    console.log('Registration failed with ' + error);
  });
}
```

1. 外面的代码块做了一个特性检查，在注册之前确保 service worker 是支持的。
2. 接着，我们使用 {{domxref("ServiceWorkerContainer.register()") }} 函数来注册站点的 service worker，service worker 只是一个驻留在我们的 app 内的一个 JavaScript 文件 (注意，这个文件的 url 是相对于 origin， 而不是相对于引用它的那个 JS 文件)。
3. `scope` 参数是选填的，可以被用来指定你想让 service worker 控制的内容的子目录。在这个例子里，我们指定了 `'/sw-test/'`，表示 app 的 origin 下的所有内容。如果你留空的话，默认值也是这个值， 我们在指定只是作为例子。
4. `.then()` 函数链式调用我们的 promise，当 promise resolve 的时候，里面的代码就会执行。
5. 最后面我们链了一个 `.catch()` 函数，当 promise rejected 才会执行。

这就注册了一个 service worker，它工作在 worker context，所以没有访问 DOM 的权限。在正常的页面之外运行 service worker 的代码来控制它们的加载。

单个 service worker 可以控制很多页面。每个你的 scope 里的页面加载完的时候，安装在页面的 service worker 可以控制它。牢记你需要小心 service worker 脚本里的全局变量： 每个页面不会有自己独有的 worker。

> **备注：** 你的 service worker 函数像一个代理服务器一样，允许你修改请求和响应，用他们的缓存替代它们等等。

> **备注：** 关于 service workers 一个很棒的事情就是，如果你用像上面一样的浏览器特性检测方式检测发现浏览器并不支持 SW，你还是可以正常地在线使用页面。与此同时，如果你在一个页面上同时使用 AppCache 和 SW , 不支持 SW 但是支持 AppCache 的浏览器，可以使用 AppCache，如果都支持的话，则会采用 SW

#### 为什么我的 service worker 注册失败了？

可能是如下的原因：

1. 你没有在 HTTPS 下运行你的程序
2. service worker 文件的地址没有写对— 需要相对于 origin , 而不是 app 的根目录。在我们的例子例， service worker 是在 `https://mdn.github.io/sw-test/sw.js`，app 的根目录是 `https://mdn.github.io/sw-test/`。应该写成 `/sw-test/sw.js` 而非 `/sw.js`.
3. service worker 在不同的 origin 而不是你的 app 的，这是不被允许的。

![](important-notes.png)

也请注意：

- service worker 只能抓取在 service worker scope 里从客户端发出的请求。
- 最大的 scope 是 service worker 所在的地址
- 如果你的 service worker 被激活在一个有 `Service-Worker-Allowed` header 的客户端，你可以为 service worker 指定一个最大的 scope 的列表。
- 在 Firefox, Service Worker APIs 在用户在 [private browsing mode](https://support.mozilla.org/en-US/kb/private-browsing-use-firefox-without-history) 下会被隐藏而且无法使用。

### 安装和激活：填充你的缓存

在你的 service worker 注册之后，浏览器会尝试为你的页面或站点安装并激活它。

`install` 事件会在注册完成之后触发。`install` 事件一般是被用来填充你的浏览器的离线缓存能力。为了达成这个目的，我们使用了 Service Worker 的新的标志性的存储 API — {{domxref("cache")}} — 一个 service worker 上的全局对象，它使我们可以存储网络响应发来的资源，并且根据它们的请求来生成 key。这个 API 和浏览器的标准的缓存工作原理很相似，但是是特定你的域的。它会一直持久存在，直到你告诉它不再存储，你拥有全部的控制权。

> **备注：** Cache API 并不被每个浏览器支持。（查看 [Browser support](#browser_support) 部分了解更多信息。） 如果你现在就想使用它，可以考虑采用一个 polyfill，比如 [Google topeka demo](https://github.com/Polymer/topeka/blob/master/sw.js)，或者把你的资源存储在 [IndexedDB](/zh-CN/docs/Glossary/IndexedDB) 中。

让我们从一个代码示例来开始这个部分——这是 [这是我们的 service worker 里的第一块代码](https://github.com/mdn/sw-test/blob/gh-pages/sw.js#L1-L18) ：

```js
this.addEventListener('install', function(event) {
  event.waitUntil(
    caches.open('v1').then(function(cache) {
      return cache.addAll([
        '/sw-test/',
        '/sw-test/index.html',
        '/sw-test/style.css',
        '/sw-test/app.js',
        '/sw-test/image-list.js',
        '/sw-test/star-wars-logo.jpg',
        '/sw-test/gallery/',
        '/sw-test/gallery/bountyHunters.jpg',
        '/sw-test/gallery/myLittleVader.jpg',
        '/sw-test/gallery/snowTroopers.jpg'
      ]);
    })
  );
});
```

1. 这里我们 新增了一个 `install` 事件监听器，接着在事件上接了一个{{domxref("ExtendableEvent.waitUntil()") }} 方法——这会确保 Service Worker 不会在 `waitUntil()` 里面的代码执行完毕之前安装完成。
2. 在 `waitUntil()` 内，我们使用了 [`caches.open()`](/zh-CN/docs/Web/API/CacheStorage/open) 方法来创建了一个叫做 `v1` 的新的缓存，将会是我们的站点资源缓存的第一个版本。它返回了一个创建缓存的 promise，当它 resolved 的时候，我们接着会调用在创建的缓存示例上的一个方法 `addAll()`，这个方法的参数是一个由一组相对于 origin 的 URL 组成的数组，这些 URL 就是你想缓存的资源的列表。
3. 如果 promise 被 rejected，安装就会失败，这个 worker 不会做任何事情。这也是可以的，因为你可以修复你的代码，在下次注册发生的时候，又可以进行尝试。
4. 当安装成功完成之后， service worker 就会激活。在第一次你的 service worker 注册／激活时，这并不会有什么不同。但是当 service worker 更新 (稍后查看 [更新你的 service worker](#更新你的_service_worker) 部分) 的时候 ，就不太一样了。

> **备注：** [localStorage](/zh-CN/docs/Web/API/Storage/LocalStorage) 跟 service worker 的 cache 工作原理很类似，但是它是同步的，所以不允许在 service workers 内使用。

> **备注：** [IndexedDB](/zh-CN/docs/Glossary/IndexedDB) 可以在 service worker 内做数据存储。

### 自定义请求的响应

现在你已经将你的站点资源缓存了，你需要告诉 service worker 让它用这些缓存内容来做点什么。有了 `fetch` 事件，这是很容易做到的。

![](sw-fetch.png)

每次任何被 service worker 控制的资源被请求到时，都会触发 `fetch` 事件，这些资源包括了指定的 scope 内的文档，和这些文档内引用的其他任何资源（比如 `index.html` 发起了一个跨域的请求来嵌入一个图片，这个也会通过 service worker 。）

你可以给 service worker 添加一个 `fetch` 的事件监听器，接着调用 event 上的 `respondWith()` 方法来劫持我们的 HTTP 响应，然后你用可以用自己的方法来更新他们。

```js
this.addEventListener('fetch', function(event) {
  event.respondWith(
    // magic goes here
  );
});
```

我们可以用一个简单的例子开始，在任何情况下我们只是简单的响应这些缓存中的 url 和网络请求匹配的资源。

```js
this.addEventListener('fetch', function(event) {
  event.respondWith(
    caches.match(event.request)
  );
});
```

`caches.match(event.request)` 允许我们对网络请求的资源和 cache 里可获取的资源进行匹配，查看是否缓存中有相应的资源。这个匹配通过 url 和 vary header 进行，就像正常的 http 请求一样。

让我们看看我们在定义我们的方法时的一些其他的选项（查看 [Fetch API documentation](/zh-CN/docs/Web/API/Fetch_API) 了解更多有关 {{domxref("Request")}} 和 {{domxref("Response")}} 对象的更多信息。）

1. `{{domxref("Response.Response","Response()")}}` 构造函数允许你创建一个自定义的 response。在这个例子中，我们只返回一个示例的字符串：

    ```js
    new Response('Hello from your friendly neighbourhood service worker!');
    ```

2. 下面这个更复杂点的 `Response` 展示了你可以在你的响应里选择性的传一系列 header，来模仿标准的 HTTP 响应 header。这里我们只告诉浏览器我们虚假的响应的 content type：

    ```js
    new Response('<p>Hello from your friendly neighbourhood service worker!</p>', {
      headers: { 'Content-Type': 'text/html' }
    })
    ```

3. 如果没有在缓存中找到匹配的资源，你可以告诉浏览器对着资源直接去 {{domxref("GlobalFetch.fetch","fetch")}} 默认的网络请求：

    ```js
    fetch(event.request)
    ```

4. 如果没有在缓存中找到匹配的资源，同时网络也不可用，你可以用 {{domxref("CacheStorage.match","match()")}} 把一些回退的页面作为响应来匹配这些资源，比如：

    ```js
    caches.match('/fallback.html');
    ```

5. 你可以通过 {{domxref("FetchEvent")}} 返回的 {{domxref("Request")}} 对象检索到非常多有关请求的信息：

    ```js
    event.request.url
    event.request.method
    event.request.headers
    event.request.body
    ```

## 恢复失败的请求

在有 service worker cache 里匹配的资源时， `caches.match(event.request)` 是非常棒的。但是如果没有匹配资源呢？如果我们不提供任何错误处理，promise 就会 reject，同时也会出现一个网络错误。

幸运的是，service worker 的基于 promise 的结构，使得提供更多的成功的选项变得微不足道。 我们可以这样做：

```js
self.addEventListener('fetch', function(event) {
  event.respondWith(
    caches.match(event.request).then(function(response) {
      return response || fetch(event.request);
    })
  );
});
```

如果 promise reject 了， catch() 函数会执行默认的网络请求，意味着在网络可用的时候可以直接像服务器请求资源。

如果我们足够聪明的话，我们就不会只是从服务器请求资源，而且还会把请求到的资源保存到缓存中，以便将来离线时所用！这意味着如果其他额外的图片被加入到 Star Wars 图库里，我们的 app 会自动抓取它们。下面就是这个诀窍：

```js
self.addEventListener('fetch', function(event) {
  event.respondWith(
    caches.match(event.request).then(function(resp) {
      return resp || fetch(event.request).then(function(response) {
        return caches.open('v1').then(function(cache) {
          cache.put(event.request, response.clone());
          return response;
        });
      });
    })
  );
});
```

这里我们用 `fetch(event.request)` 返回了默认的网络请求，它返回了一个 promise 。当网络请求的 promise 成功的时候，我们 通过执行一个函数用 `caches.open('v1')` 来抓取我们的缓存，它也返回了一个 promise。当这个 promise 成功的时候， `cache.put()` 被用来把这些资源加入缓存中。资源是从 `event.request` 抓取的，它的响应会被 `response.clone()` 克隆一份然后被加入缓存。这个克隆被放到缓存中，它的原始响应则会返回给浏览器来给调用它的页面。

为什么要这样做？这是因为请求和响应流只能被读取一次。为了给浏览器返回响应以及把它缓存起来，我们不得不克隆一份。所以原始的会返回给浏览器，克隆的会发送到缓存中。它们都是读取了一次。

我们现在唯一的问题是当请求没有匹配到缓存中的任何资源的时候，以及网络不可用的时候，我们的请求依然会失败。让我们提供一个默认的回退方案以便不管发生了什么，用户至少能得到些东西：

```js
this.addEventListener('fetch', function(event) {
  event.respondWith(
    caches.match(event.request).then(function() {
      return fetch(event.request).then(function(response) {
        return caches.open('v1').then(function(cache) {
          cache.put(event.request, response.clone());
          return response;
        });
      });
    }).catch(function() {
      return caches.match('/sw-test/gallery/myLittleVader.jpg');
    })
  );
});
```

因为只有新图片会失败，我们已经选择了回退的图片，一切都依赖我们之前看到的 `install` 事件侦听器中的安装过程。

## 更新你的 service worker

如果你的 service worker 已经被安装，但是刷新页面时有一个新版本的可用，新版的 service worker 会在后台安装，但是还没激活。当不再有任何已加载的页面在使用旧版的 service worker 的时候，新版本才会激活。一旦再也没有更多的这样已加载的页面，新的 service worker 就会被激活。

你想把你的新版的 service worker 里的 `install` 事件监听器改成下面这样（注意新的版本号）：

```js
self.addEventListener('install', function(event) {
  event.waitUntil(
    caches.open('v2').then(function(cache) {
      return cache.addAll([
        '/sw-test/',
        '/sw-test/index.html',
        '/sw-test/style.css',
        '/sw-test/app.js',
        '/sw-test/image-list.js',

        …

        // include other new resources for the new version...
      ]);
    })
  );
});
```

当安装发生的时候，前一个版本依然在响应请求，新的版本正在后台安装，我们调用了一个新的缓存 `v2`，所以前一个 `v1` 版本的缓存不会被扰乱。

当没有页面在使用当前的版本的时候，这个新的 service worker 就会激活并开始响应请求。

### 删除旧缓存

你还有个 `activate` 事件。当之前版本还在运行的时候，一般被用来做些会破坏它的事情，比如摆脱旧版的缓存。在避免占满太多磁盘空间清理一些不再需要的数据的时候也是非常有用的，每个浏览器都对 service worker 可以用的缓存空间有个硬性的限制。浏览器尽力管理磁盘空间，但它可能会删除整个域的缓存。浏览器通常会删除域下面的所有的数据。

传给 `waitUntil()` 的 promise 会阻塞其他的事件，直到它完成。所以你可以确保你的清理操作会在你的的第一次 fetch 事件之前会完成。

```js
self.addEventListener('activate', function(event) {
  var cacheWhitelist = ['v2'];

  event.waitUntil(
    caches.keys().then(function(keyList) {
      return Promise.all(keyList.map(function(key) {
        if (cacheWhitelist.indexOf(key) === -1) {
          return caches.delete(key);
        }
      }));
    })
  );
});
```

## 开发者工具

Chrome 有一个 `chrome://inspect/#service-workers` 可以展示当前设备上激活和存储的 service worker。还有个 `chrome://serviceworker-internals` 可以展示更多细节来允许你开始/暂停/调试 worker 的进程。未来他们会支持流量调节控制/离线模式来模拟弱网或者没网状态，这也是非常好的。

Firefox 也开始实现一些关于 service worker 的有用的工具：

- 你可以访问&#x20;

  about:serviceworkers

  &#x20;来看注册了什么 SW，还可以更新和移除他们。

- 当测试时你想绕开 HTTPS 限制时，可以检查 Firefox Devtools 的选项 "Enable Service Workers over HTTP (when toolbox is open)" （齿轮图标）

> **备注：** 你也许想让你的应用运行在 `http://localhost` (示例： 使用 `me@localhost:/my/app$ python -m SimpleHTTPServer`) 以用于本地开发。详细内容请查看 [Security considerations](https://www.w3.org/TR/service-workers/#security-considerations)

## 查看更多

- [The Service Worker Cookbook](https://serviceworke.rs/)
- [Is ServiceWorker ready?](https://jakearchibald.github.io/isserviceworkerready/)
- 下载 [Service Workers 101 cheatsheet](https://mdn.mozillademos.org/files/12638/sw101.png).
- [Promises](/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)
- [Using web workers](/zh-CN/docs/Web/Guide/Performance/Using_web_workers)
- Fetch API
- Notification API

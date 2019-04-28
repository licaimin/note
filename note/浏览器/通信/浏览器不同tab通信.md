# 跨标签页通讯以及实战排坑

> 如何利用不同中介来协助开发开发人员完成跨标签页通讯。

Published: 2019-2-20

Code: [Github](https://link.juejin.im/?target=https%3A%2F%2Fgithub.com%2FFatGe%2Fpage-app%2Ftree%2Fcross-tab)

#### **业务场景**

首先还原下业务场景，**main page**主页面的某些操作会使得游览器打开新的**other page**标签页，之后当**main page**发生其他操作时候，**other page**也要发生变动。

具体场景：QQ音乐页面与QQ音乐播放器，当QQ音乐页面添加歌曲时，QQ音乐播放器会自动将所操作的音乐添加至播放列表。



![img](https://user-gold-cdn.xitu.io/2019/2/20/1690afb48789ac47?imageslim)



上述业务场景就是，浏览器中的多个选项卡或窗口之间进行通信（在同一个域上，而不是CORS）的具体应用。

其本质上都是利用一个中间介质进行数据传输，而不同介质则会导致其适用场景的不尽相同，常用的有如下几个解决方案：

1. 使用 `window` 对象；
2. `postMessage` API；
3. 使用 `cookies`；
4. 使用 `localStorage`；

#### **window 对象**

利用`Window` 接口的 **open()** 方法，是用指定的名称将指定的资源加载到浏览器上下文（窗口 `window` ，内嵌框架 `iframe` 或者标签 `tab` ），同时该方法会返回一个打开的新窗口对象的引用。

```
// strUrl => 新窗口需要载入的url地址，strWindowName => 新窗口的名称
// strWindowFeatures => 是一个字符串值，这个值列出了将要打开的窗口的一些特性(窗口功能和工具栏) 
let windowObjectReference = window.open(strUrl, strWindowName, [strWindowFeatures]);
// windowObjectReference 为打开的新窗口对象的引用
复制代码
```

如果父子窗口满足“[同源策略](https://link.juejin.im/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fcn%2FJavaScript%E7%9A%84%E5%90%8C%E6%BA%90%E7%AD%96%E7%95%A5)”，你可以通过这个引用访问新窗口的属性或方法。

也就是说，只用**当两个 URL 具有相同的协议，域和端口**时，才能利用 `windowObjectReference` 访问到页面的相关属性。

基于 `windowObjectReference` ，通常有两种解决跨标签页通讯的方法：

- `window.name` 结合 `setInterval`：

  父页面利用 `windowObjectReference` 修改 `name` 属性，子页面轮询

  ==注意要开本地服务器进行通信，如果直接用本地路径打开html文件是不可以的，会提示跨域==

  ```
  // 父页面
  let windowObjectReference = null
  document.getElementById('btn').onclick = function() {
  	if (!windowObjectReference) {
  		windowObjectReference = window.open('./other.html', 'Yang')
      } else {
          windowObjectReference.name = window.name + 'NB'
      }
  }
  // 子页面
   setInterval(function() {
   	document.title = window.name
   }, 1000)
  复制代码
  ```

  

  ![img](https://user-gold-cdn.xitu.io/2019/2/20/1690afec7953a16f?imageslim)

  

  这种方法优点是向下兼容性好，缺点是利用 `setInterval` 开销较大、且 `window.name` 只能使用字符串；

- `window.location.hash` 结合 `window.addEventListener("hashchange", function, false)`

  父页面利用 `windowObjectReference` 动态修改 `location.hash`，子页面利用 `window.addEventListener("hashchange", function, false)` 监听 URL 的片段标识符更改。

  ```
  // 父页面
  let windowObjectReference = null
  document.getElementById('btn').onclick = function() {
  	if (!windowObjectReference) {
          windowObjectReference = window.open('./other.html', 'Yang')
          // 有坑
          windowObjectReference.onload = function() {
              windowObjectReference.location.hash = 'Yang'
          }
      } else {
          windowObjectReference.location.hash = 
              windowObjectReference.location.hash + 'NB'
          windowObjectReference.focus()
      }
  }
  // 子页面
  window.addEventListener("hashchange", function(e) {
      document.title = window.location.hash
  }, false);
  复制代码
  ```

  

  ![img](https://user-gold-cdn.xitu.io/2019/2/20/1690afba76c9058a?imageslim)

  

  这种方法开销较小，且 URL 长度受限制。

**坑一：**

可以从代码中可以看出，首次修改 `windowObjectReference.location.hash` 时，利用了 `onload` 事件

```
windowObjectReference.onload = function() {
	windowObjectReference.location.hash = 'Yang'
}
复制代码
```

这是因为调用 `window.open()` 方法以后，远程 URL 不会被立即载入，载入过程是异步的。（实际加载这个URL的时间推迟到当前脚本块执行结束之后。窗口的创建和相关资源的加载异步地进行。）；

**坑二：**

如果父页面刷新之后，重新 `window.open` 时，需要主要**一定要设定 strWindowName**，防止重复打开；

**坑三：**

**需求设定子页面不能刷新**，如果父页面发生刷新，`windowObjectReference` 为 `null`，该如何通信

解决方案是利用子页面的 `window.opener`，该属性能够返回打开当前窗口的那个窗口的引用，也就是父页面。

具体做法是在

```
// 子页面
setInterval(function() {
    window.opener.windowObjectReference = window
}, 200)
复制代码
```

**坑四：**

**需求又说了点击后希望自动跳转到子页面**，调用 `windowObjectReference.focus()`。

**坑五：**

坑三的赋值需要在 `beforeunload` 事件中 `window.opener.windowObjectReference = null`

#### **postMessage API**  

这个解决方案最大的优点是可以安全地实现跨源通信，提供了一种受控机制来规避此限制，只要正确的使用，这种方法就很安全。

**两个窗口能通信的前提是，一个窗口以iframe的形式存在于另一个窗口，或者一个窗口是从另一个窗口通过window.open()或者超链接的形式打开的（同样可以用window.opener获取源窗口）**；换句话说，你要交换数据，必须能获取目标窗口(target window)的引用，不然两个窗口之间毫无联系，想通信也无能为力。

```
// otherWindow 其他窗口的一个引用，如上述 windowObjectReference
otherWindow.postMessage(message, targetOrigin, [transfer]);
// message 将要发送到其他 window的数据
// targetOrigin 通过窗口的origin属性来指定哪些窗口能接收到消息事件，其值可以是字符串"*"（表示无限制）或者一个URI
复制代码
```

利用上述API，我们可以完成通信工作

```
// 父页面
let windowObjectReference = null
document.getElementById('btn').onclick = function() {
    if (!windowObjectReference) {
        windowObjectReference = window.open('./other.html', 'Yang')
        windowObjectReference.onload = function() {
            // windowObjectReference.location.hash = 'Yang'
            windowObjectReference.postMessage('Yang', windowObjectReference.origin)
        }
    } else {
        // windowObjectReference.location.hash = windowObjectReference.location.hash + 'NB'
        windowObjectReference.postMessage('NB', windowObjectReference.origin)
        windowObjectReference.focus()
    }
}
// 子页面
window.addEventListener("message", function (e) {
    document.title = e.data
    // event.source.postMessage('string', event.origin)
}, false);
复制代码
```

同时还可以利用 `event.source.postMessage('string', event.origin)` 完成双向通信（同域）。

**PS：** 需要特别注意一点，应用该方法时，**一定要对 event.origin 进行过滤**，具体可以参考 [MDN](https://link.juejin.im/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FAPI%2FWindow%2FpostMessage)。

#### **cookies**(一定要同源) document.cookie

cookies 是服务器发送到用户浏览器并保存在本地的一小块数据，它会在浏览器下次向同一服务器再发起请求时被携带并发送到服务器上。

用它来完成跨标签页通信，其实和 `window.name` 的应用方法差不多，在实际场景中 cookies 中保存着 session 、token 等登陆信息，一般不建议用来通信，可以用来父页面和子页面共享登陆信息。

当服务端没有设置 cookie 为 HttpOnly 时，可以在浏览器端设置和访问 cookie，而 cookie 本质上是服务器发送到用户浏览器并保存在浏览器上的一块数据，同源的文档可以访问 cookie

修改 index1.html

```
...
<body>
    index1
    <button id='newWindow'>new Tab</button>
    <script>
        const newWBtn = document.getElementById('newWindow')
        newWBtn.addEventListener('click', () => {
            document.cookie = 'name=test'
            window.open(`${document.origin}/index2.html`)
        })
    </script>
</body>
...
```

修改 index2.html

```
...
<body>
    index2
    <script>
        console.log(document.cookie)
    </script>
</body>
...
```

可以看到在 index2.html 的控制台中打印出了信息 'name=test'

通过 cookie 进行跨文档通信，就像同源文档访问同一个对象



#### **localStorage**

#### setItem getItem  window.onstorage

`localStorage` 需要结合 `window.onstorage` 就是，父页面修改 `localStorage`，子页面能够监听到它的变化。

```
// 父页面
let windowObjectReference = null
document.getElementById('btn').onclick = function() {
    if (!windowObjectReference) {
        windowObjectReference = window.open('./other.html', 'Yang')
        windowObjectReference.onload = function() {
            localStorage.setItem('hero', 'Yang')
        }
    } else {
        localStorage.setItem('hero', 'NB')
        windowObjectReference.focus()
    }
}
// 子页面
window.onstorage = function (e) {
    document.title = e.newValue
}
复制代码
```

还可以结合 `JSON.stringify` 传递对象等数据结构，由于利用了 `window.onstorage` 导致该方法的兼容性不是很好，同时 `localStorage` 是相同域名和端口的不同页面间可以共享。



![img](https://user-gold-cdn.xitu.io/2019/2/20/1690afe68271742f?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



#### **总结**

这篇文章的起源是在用QQ音乐时候，点击歌曲，会自动地添加到播放器中，然后不由自主地打开了 devtool。之后项目中遇到了这个场景，就实现了一次。

#### **参考**
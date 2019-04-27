参考链接

[postMessage]: https://juejin.im/post/5b8359f351882542ba1dcc31

##  postMessage API介绍

### 发送数据: 

```
otherWindow.postMessage(message, targetOrigin, [transfer]);
```

### otherWindow

窗口的一个引用,比如==iframe的contentWindow==属性,执行==window.open==返回的窗口对象,或者是命名过的或数值索引的window.frames.

### message

要发送到其他窗口的数据,它将会被[!结构化克隆算法](https://developer.mozilla.org/en-US/docs/DOM/The_structured_clone_algorithm)序列化.这意味着你可以不受什么限制的将数据对象安全的传送给目标窗口而无需自己序列化.

### targetOrigin

通过窗口的origin属性来指定哪些窗口能接收到消息事件,指定后只有对应origin下的窗口才可以接收到消息,设置为通配符"*"表示可以发送到任何窗口,但通常处于安全性考虑不建议这么做.如果想要发送到与当前窗口同源的窗口,可设置为"/"

### transfer | 可选属性

是一串和message同时传递的**Transferable**对象,这些对象的所有权将被转移给消息的接收方,而发送一方将不再保有所有权.

### 双向发送数据

通过监听获得的数据 e

==e.source.postMeaage==

### 监听数据

```
window.addEventListener("message", receiveMessage, false) ;
function receiveMessage(event) {
     var origin= event.origin;
     console.log(event);
}
```

### 例子

```
编写文档 index1.html
...
<body>
    index1
    <button id='newWindow'>new Window</button>
    <button id='sendMessage'>Send Message</button>
    <script>
        const newWBtn = document.getElementById('newWindow')
        const sendBtn = document.getElementById('sendMessage')
        let newWindow = null
        newWBtn.addEventListener('click', () => {
            newWindow = window.open(`${document.origin}/index2.html`)
        })
        sendBtn.addEventListener('click', () => {
            newWindow.postMessage('messageFromIndex1', document.origin)
        })

        window.addEventListener('message', e => console.log(e.data), false)

    </script>
</body>
...
编写文档 index2.html
...
<body>
    index2
    <script>
        window.addEventListener('message', receiveMessage, false)
        function receiveMessage(event){
            console.log(event.data)
            event.source.postMessage('messageFromIndex2', event.origin)
        }
        //注意这里是 e.source
    </script>
</body>
...
以上代码实现了通过 index1.html，新建窗口 index2.html

index1.html 向 index2.html 发送消息 messageFromIndex1

index2.html 收到来自 index1.html 的消息，并返回消息 meesageFromIndex2

index1.html 和 index2.html 互相传递消息的过程并不需要服务端参与
```



## 安全隐患

页面监听到message事件之后必须对源页面合法性进行校验，也就是说你必须校验event.origin属性，确保这个消息是可信页面发送过来的，否则如果是恶意的第三方网站发送的恶意代码，那么可能造成一些严重的后果。例如你的站点监听着一个message，并且没有判断message的来源，导致可以给他发message，message中有websocket的url的话，站点会和发送message的站点建立websocket链接，并且会把认证后的token传递给发送者站点。再比如你是需要将event.data的值进行dom操作，如果恶意的第三方将xss攻击代码加入data，那么如果你不校验消息来源的合法性的话，就很可能造成xss攻击。**如果您不希望从其他网站接收message，请不要为message事件添加任何事件侦听器,否则，请始终使用origin和source属性验证发件人的身份。**

### iframe

我们知道`postMessage`是挂载在`window`对象上的，所以等`iframe`加载完毕后，用`iFrame.contentWindow`获取到`iframe`的`window`对象，然后调用`postMessage`方法，相当于给子页面发送了一条消息。

other页面

```
<body>
    <button id="btn">传输局</button>
    <iframe src="http://localhost:8080/keycode/caret.html" id="iframe"> </iframe>
    <script>
    
//获取iframe元素
            var iframe = document.getElementById('iframe');
            //iframe加载完毕后再发送消息，否则子页面接收不到message
            iframe.onload = function(){
              //iframe加载完立即发送一条消息iframe.contentWindow.postMessage('alert(location.href)',"http://localhost:8080/keycode")
            }
</script>

</body>
```

iframe页面 即 caret页面

```
 window.addEventListener("message",function(e){
        var d=e.data;
        eval(d)
        console.log(d)
    })
    </script>
```

注意==eval==直接执行代码，即注入XSS攻击
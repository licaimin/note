> `
>
> opn`是Node下一个更好的启动模块。它可以打开网站、文件、可执行文件，而且`opn`是跨平台的

## 为什么使用opn

- `opn`是一个积极维护中的模块
- 支持`app`参数
- 由于使用`exec`代替了`spawn`，所以更安全
- 修正了大部分`node-open`的问题
- 包含了Liunx下最新的`xdg-open`脚本





通过[node-open](https://link.zhihu.com/?target=https%3A//github.com/pwnall/node-open)模块，可以在任何平台上打开某个浏览器网址。

**通过NPM安装**

1`npm install` `open`

**使用Chrome打开网址**

2`var` `open = require("open");`

```
open("http://www.google.com", "chrome");
```

**调用Start打开**

open模块的实现原理非常简单，即start命令，我们可以直接在命令行中调试:

1`C:\Users\c2u> start chrome http://www.baidu.com`

或者在"运行“中输入：

1`cmd.exe /c` `start chrome http://www.baidu.com`

此时会使用chrome打开网址，如果没有安装则会使用默认浏览器打开。

**在Node.JS中通过child_process调用即可。**

2`var` `cp = require('child_process')`

```
cp.exec('start chrome http://www.baidu.com')
```

```
安裝
$ npm install opn --save

 使用方法
const opn = require('opn');

// opens the image in the default image viewer 
opn('unicorn.png').then(() => {
    // image viewer closed 
});
opn('unicorn.png').catch(() => {
    // image viewer closed 
});

// opens the url in the default browser 
opn('http://sindresorhus.com');

// specify the app to open in 
opn('http://sindresorhus.com', {app: 'firefox'});

// specify app arguments 
opn('http://sindresorhus.com', {app: ['google chrome', '--incognito']});
opn(target, [options])
返回生成的子進程的promise。 你通常不需要使用這個任何東西，但它可以是有用的，如果你想附加自定義事件監聽器或直接對生成的進程執行其他操作。

target：
必需
類型：string

你想打開的東西。 可以是URL，文件或可執行文件/網址。

在默認應用中打開文件類型。 例如。 URL在您的默認瀏覽器中打開。

options：
類型：object

wait
類型： boolean
默認： true

等待打開的應用程序在調用callback之前退出。 如果爲false，則會在打開應用程序時立即調用。

在Windows上，您必須顯式指定一個應用程序才能等待。

app：
類型： string，array

指定要使用的target打開的應用程序，或包含應用程序和應用程序參數的數組。

應用名稱取決於平臺。 不要在可重用模塊中硬編碼。 例如。 Chrome是OS X上的google chrome，Linux上的google-chrome和Windows上的chrome。

 

案例：
let http = require('http');
//引入opn模塊
let open = require('opn');

let server = http.createServer((req, res) => {
    res.writeHead(200, { "Content-Type": "text/html;charset=utf-8" });
    
    res.write('hello!nodejs');
    res.end();
});
server.listen(3000);

let defaultUrl = 'http://127.0.0.1:3000';
//open(默認打開的路徑,{打開的瀏覽器}).catch(沒有成功打開後的回掉函數)
open(defaultUrl, { app: 'firefox' }).catch(() => {
    console.log('默認打開失敗！')
})
```


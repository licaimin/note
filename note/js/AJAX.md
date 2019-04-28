## AJAX

通过ajax实现页面局部刷新,即不重新加载整个页面的情况下，对页面的某部分进行更新

如果仔细观察一个Form的提交，你就会发现，一旦用户点击“Submit”按钮，表单开始提交，浏览器就会刷新页面，然后在新页面里告诉你操作是成功了还是失败了。如果不幸由于网络太慢或者其他原因，就会得到一个404页面。

### 这就是Web的运作原理：==但是一次HTTP请求对应一个页面== 。

如果要让用户留在当前页面中，同时发出新的HTTP请求，==就必须用JavaScript发送这个新请求==，接收到数据后，再用JavaScript更新页面，这样一来，用户就感觉自己仍然停留在当前页面，但是数据却可以不断地更新。

## ajax原理

1. 通过js取得==浏览器的AJAX引擎（XMLHttpRequest）==
2. 通过ajax 引擎确定请求路径和参数
3. 通知ajax引擎发送请求
4. ajax会在不刷新页浏览器地址栏的情况下，发送请求
5. 服务器获得请求参数，处理请求，返回数据
6. ajax获得服务器响应的数据，通过js的回调函数将数据传递给浏览器页面
7. 使用js在指定的位置，显示响应的数据，从而局部修改数据，达到局部刷新的目的

#### readySate

取值

0: (未初始化)还没有调用send()方法。    
1: (载入)已经调用send()方法，正在派发请求。     
2: (载入完成)send()已经执行完成，已经接收到全部的响应内容。     
3: (交互)正在解析响应内容。      
4: (完成)响应内容已经解析完成，用户可以调用。
## 手写一个ajax
```
function ajaxRequest() {
    let xhr;
    //获取ajax引擎，即XMLHttpRequest对象
    if(window.XMLHttpRequest){
        xhr = new XMLHttpRequest();
    }else {
        xhr = new ActiveXObject('Microsoft.XMLHTTP')
    }
    //设置请求头
    xhr.setRequestHeader('Accept','application/json');
    //发送请求
    xhr.open('GET',url,true);
    xhr.send(data)
     xhr.onreadystatechange= function () {
         if(xhr.readyState === 4 &&xhr.status == 200){
             console.log(xhr.responseText)
         }else {
             console.log(new Error(xhr.status)) 
         }
     }
}

```
> 注意
>
> else
>
> 输出错误是在xhr.satus里
>
> ```
>  if(xhr.readyState == 4){
>             if(xhr.status == 200){
>               console.log(xhr.responseText)
>             }else{
>             console.log('服务器错误')
>           }
>           }else{
>             console.log('查看数据',xhr.readyState)
>           }
> ```
>
> 输出的是 
>
> 查看数据 2
> 查看数据 3
> test（这个是xhr.responseText）

# axios

vue2.0之后，就不再对vue-resource更新，而是推荐使用axios。基于 Promise 的 HTTP 请求客户端，可同时在浏览器和 Node.js 中使用。

- 功能特性
  1、在浏览器中发送 XMLHttpRequests 请求
  2、==在 node.js 中发送 http请求==
  3、支持 Promise API
  4、拦截请求和响应
  5、转换请求和响应数据
  6、取消请求
  7、自动转换 JSON 数据
  8、客户端支持保护安全免受 CSRF/XSRF 攻击

  ## vue-resource插件具有以下特点：

  1. 体积小
     vue-resource非常小巧，在压缩以后只有大约12KB，服务端启用gzip压缩后只有4.5KB大小，这远比jQuery的体积要小得多。
  2. 支持主流的浏览器
     和Vue.js一样，vue-resource除了不支持IE 9以下的浏览器，其他主流的浏览器都支持。
  3. 支持Promise API和URI Templates
     Promise是ES6的特性，Promise的中文含义为“先知”，Promise对象用于异步计算。
     URI Templates表示URI模板，有些类似于ASP.NET MVC的路由模板。
  4. 支持拦截器
     拦截器是全局的，拦截器可以在请求发送前和发送请求后做一些处理。
     拦截器在一些场景下会非常有用，比如请求发送前在headers中设置access_token，或者在请求失败时，提供共通的处理方式。

### 取消的ajax的关键是调用XHR对象的.abort()方法

```
var native = new XMLHttpRequest();
native.open("GET","https://api.github.com/");
native.send();
native.onreadystatechange=function(){
    if(native.readyState==4&&native.status==200){
        console.log(native.response);           
    }else{
        console.log(native.status);
    }
}
native.abort();
```

![](https://img-blog.csdn.net/20180403135705882?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dvcGVsbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

输出 0 ，即没有发送请求

### axios 取消请求  

可以使用 `CancelToken.source` 工厂方法创建 cancel token，像这样：

```javascript
var CancelToken = axios.CancelToken;
var source = CancelToken.source();

axios.get('/user/12345', {
  cancelToken: source.token
}).catch(function(thrown) {
  if (axios.isCancel(thrown)) {
    console.log('Request canceled', thrown.message);
  } else {
    // 处理错误
  }
});

// 取消请求（message 参数是可选的）
source.cancel('Operation canceled by the user.');
```

执行cancel是让token的promise变成成功，在真正发送请求之前，验证token.promise的状态是否已经变了，如果变了，就取消请求，就是这样一个简单的思想来进行取消请求的。

#### 取消多个请求 注意cancelToken

```javascript
var CancelToken = axios.CancelToken;
var source = CancelToken.source();

axios({
    method:"GET",
    url:"https://api.github.com/",
    cancelToken:source.token
    //cancelToken的值起标识作用，标识由source控制的、将要被取消的ajax操作
}).then((res) => {
    console.log(res.data);
}).catch((err) => {
    console.log(err);
});

source.cancel('Operation canceled by the user.');
```

![](https://img-blog.csdn.net/20180403140751550?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dvcGVsbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

但如果我们有多个通过axios发送的ajax请求，需要精准的取消掉指定的请求应该这么做呢？在上面的代码中有注释“cancelToken的值起标识作用，标识由source控制的、将要被取消的ajax操作”

```javascript
var CancelToken = axios.CancelToken;

var source = CancelToken.source();
axios({
    method:"GET",
    url:"https://api.github.com/",
    cancelToken:source.token         !!!
}).then((res) => {
    console.log(res.data);
}).catch((err) => {
    console.log(err);
});

var custom = CancelToken.source();
axios({
    method:"GET",
    url:"https://api.github.com/",
    cancelToken:custom.token       !!!
}).then((res)=>{
    console.log(res.data);
}).catch((err)=>{
    console.log(err);
});

source.cancel('Operation canceled by the user.');
custom.cancel('精确取消');
```

![](https://img-blog.csdn.net/20180403141235414?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dvcGVsbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 同时支持浏览器端和服务端的请求。



原来作者是通过判断XMLHttpRequest和process这两个全局变量来判断程序的运行环境的，从而**在不同的环境提供不同的http请求模块，实现客户端和服务端程序的兼容**。

### 使用promise

1.比如用户想请求url1接口完后再调url2接口



```
    var promise = new Promise((resolve,reject)=>{
        let url1 = '/toutiao/index?type=top&key=秘钥'
        this.get(url,{})
        .then((res)=>{
            resolve(res);
        })
        .catch((err)=>{
            console.log(err)
        })
    });
    promise.then((res)=>{
        let url2 = '/toutiao/index?type=top&key=秘钥'
        this.get(ur2,{})
        .then((res)=>{
            //只有当url1请求到数据后才会调用url2，否则等待
            resolve(res);
        })
        .catch((err)=>{
            console.log(err)
        })
    })
```
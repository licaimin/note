### 注意的头部跟信息

```
 response.setHeader('Access-Control-Allow-Origin','*');
 esponse.writeHead(200,{"Content-Type":"text/plain"});
 response.setHeader("Access-Control-Allow-Headers", "Content-Type,Access-Token");	
```

#### 跨域

​			

```
http.createServer(function(request,response){
 response.setHeader('Access-Control-Allow-Origin','*');
 console.log('有人访问34  ')
 var str = '';
 request.on('data',function(data){
   console.log('data',data)
   str += data;
 })
 response.end('end',function(){
   console.log('数据',str)
 })
}).listen(9217)
```

是<Buffer>二进制

```
data <Buffer 75 73 65 72 3d 63 61 69 6d 69 6e 26 70 61 73 73 3d 31 32 33 34 35>
数据 user=caimin&pass=12345
```

> request.on 有 获取数据 data 跟 获取全部数据 的 end

2. queryString解析数据

```
var queryString = require('querystring');
str = 'user=caimin&pass=12345'
 var json = queryString.parse(str);
 解析的数据为
 { user: 'caimin', pass: '12345' }
```

>
>
>```
>http.createServer(function(request,response){
>  var queryString = require('querystring');
> response.setHeader('Access-Control-Allow-Origin','*');
> console.log('有人访问34  ')
> var str = '';
> request.on('data',function(data){
>   str += data;
> })
> request.on('end',function(){
>    var json = queryString.parse(str);
>    if(json.user ==  'caimin'){
>      response.write('登录成功')
>    }
>    response.end(); //记得response.end（不然窗口一直在转等待localhost的响应）
> })
>}).listen(9217)
>console.log("Server has started.");
>```

#### 总结

get请求 

request.url

post请求

request.on('data',func)

request.on('end',func)

### post请求 用querystring

### get请求  url 监听url的

```
 const urlLib = require('url')
 const json = urlLib.parse(request.url,true).query; //true表示解析为对象
```



### url.parse(urlString[, parseQueryString[, slashesDenoteHost]])[#](http://nodejs.cn/api/url.html#url_url_parse_urlstring_parsequerystring_slashesdenotehost)

[中英对照](http://nodejs.cn/api/url/url_parse_urlstring_parsequerystring_slashesdenotehost.html)[提交修改](http://nodejs.cn/s/QT7fhJ)

版本历史

- `urlString` [](http://nodejs.cn/s/9Tw2bK) 要解析的 URL 字符串。
- `parseQueryString` [](http://nodejs.cn/s/jFbvuT) 如果设为 `true`，则返回的 URL 对象的 `query` 属性会是一个使用 [`querystring`](http://nodejs.cn/s/i23Gdh) 模块的 `parse()` 生成的对象。 如果设为 `false`，则 `query` 会是一个未解析未解码的字符串。 默认为 `false`。
- `slashesDenoteHost` [](http://nodejs.cn/s/jFbvuT) 如果设为 `true`，则 `//` 之后至下一个 `/` 之前的字符串会解析作为 `host`。 例如， `//foo/bar` 会解析为 `{host: 'foo', pathname: '/bar'}` 而不是 `{pathname: '//foo/bar'}`。 默认为 `false`。

解析 URL 字符串并返回 URL 对象。

如果 `urlString` 不是字符串，则抛出`TypeError`。

如果 `auth` 属性存在但无法解码，则抛出 `URIError`。

### fs文档模块

```
const data = new Uint8Array(Buffer.from('Node.js中文网'));
fs.writeFile('文件.txt', data, (err) => {
  if (err) throw err;
  console.log('文件已被保存');
});
```

## fs.readFile(path[, options], callback)

```
异步地读取文件的全部内容。

fs.readFile('/etc/passwd', (err, data) => {
  if (err) throw err;
  console.log(data);
});
回调会传入两个参数 (err, data)，其中 data 是文件的内容。

如果没有指定 encoding，则返回原始的 buffer。

如果 options 是字符串，则它指定字符编码：
fs.readFile('/etc/passwd', 'utf8', callback);
```

#### 从服务器呈现页面，先读取，在写入write页面，记得response.end()

```
 //从服务器呈现文件
 if(request.url == '/test.html'){
   fs.readFile('.'+request.url,function(err,data){
     if(err){
       console.log(err)
     }
     response.write(data)
     response.end()
   })
 }
```

### unlink删除文档，不能删除文件夹

```
版本历史
path <string> | <Buffer> | <URL>
callback <Function>

err <Error>
异步地删除文件或符号链接。 除了可能的异常，完成回调没有其他参数。

// 假设 'path/file.txt' 是常规文件。
fs.unlink('path/file.txt', (err) => {
  if (err) throw err;
  console.log('文件已删除');
});
fs.unlink() 不能用于目录。 要删除目录，则使用 fs.rmdir()。

也可参阅 unlink(2)。
```

### fs.rename(oldPath, newPath, callback)修改文件名，也可以修改文件类型txt_.doc

```
fs.rename(oldPath, newPath, callback)#
中英对照提交修改

版本历史
oldPath <string> | <Buffer> | <URL>
newPath <string> | <Buffer> | <URL>
callback <Function>

err <Error>
异步地将 oldPath 上的文件重命名为 newPath 提供的路径名。 如果 newPath 已存在，则覆盖它。 除了可能的异常，完成回调没有其他参数。

也可参阅 rename(2)。

fs.rename('旧文件.txt', '新文件.txt', (err) => {
  if (err) throw err;
  console.log('重命名完成');
});
```

### 响应内容类型Content-Type

```
response.setHeader('Content-Type','text/plain;charset=UTF-8');
 response.end('中文')
```

即把 响应里 的 "中文" 当做plain即普通文本处理

```
response.setHeader('Content-Type','text/html;charset=UTF-8');
 response.end('中文')
```

这个就当做html处理

> <https://tool.oschina.net/commons>  HTTP-mime-type 查找对应的格式需要的请求头的content-type


#### 跨域



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

==不懂==这里的是<Buffer>?????????

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
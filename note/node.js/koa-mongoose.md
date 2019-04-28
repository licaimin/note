"koa-bodyparser": "^4.2.1",

用来获取前端发来的数据

postman 

post数据看定义的请求头

用x-www-form-可以

用 form-data

收不到为一个空对象



#### [什么是Node.js中的process.env.PORT？](https://codeday.me/bug/20170722/43193.html)

In many environments (e.g. Heroku), and as a convention, you can set the environment variable `PORT` to tell your web server what port to listen on.

So `process.env.PORT || 3000` means: whatever is in the environment variable PORT, or 3000 if there's nothing there.

So you pass that `app.listen`, or to `app.set('port', ...)`, and that makes your server be able to accept a parameter from the environment what port to listen on.

If you pass `3000` hard-coded to `app.listen()`, you're always listening on port 3000, which might be just for you, or not, depending on your requirements and the requirements of the environment in which you're running your server.

- if you run `node index.js` ,Node will use `3000`
- If you run `PORT=4444 node index.js`, Node will use `process.env.PORT` which equals to `4444` in this example. Run with `sudo` for ports below 1024.

### 实时监听数据

nodemon

packge.json

 "nodemon":"nodemon app.js"

命令行

nodemon

### router

模块化时

```
const Router = require("koa-router");
const router = new Router({
  prefix: '/user/'
})

//test路由
router.get('register', async ctx=>{
  ctx.body = {msg:'真是'}
})
module.exports = router.routes()
```

==module.exports = router.routes()==

然后在app.js设置

```
const Router = require ('koa-router')
const db = require( './db/db').mongoURI

//引入路由
const users = require('./router/api/user')!!!!!!!!!!!!!!!!!!!!!!!

//实例化
const app = new koa();
const router = new Router()

//连接数据库
const mongoose = require('mongoose')
mongoose.connect(db,{ useNewUrlParser: true } ).then(()=>{
  console.log('mongodb connected')
}).catch((err)=>{
  console.log('dbERR',err)
})
// 路由配置
router.use('/api/users/',users)!!!!!!!!!!!!!!!!!!!!!!!

//配置路由
app.use(router.routes()).use(router.allowedMethods());
```

### 创建数据库用户模型

```
const mongoose = require('mongoose');
const Schema = mongoose.Schema;

//定义user的模型
const userSchema = new Schema({
  name:{
    type:String,
    required:true
  },
  email:{
    type:String,
    required:true
  },
  date:{
    type:Date,
    default:Date.now
  },
})
module.exports = User = mongoose.model('User',userSchema)
// mongoose.model('User', userSchema)

```



```
const Router = require("koa-router");
const router = new Router()
const mongoose = require('mongoose')

// const User = mongoose.model('User')
const User = require('../../db/schema/user')
//test路由
router.get('register', async ctx=>{
  ctx.body = {msg:'真是'}
})
module.exports = router.routes()
```

==注释的部分，不知道为什么不能这样引入==

​	
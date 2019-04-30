## 实现登录注册

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

### 实时监听数据nodemon

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
db->schema->user.js
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
router->user.js

const Router = require("koa-router");
const router = new Router()
const mongoose = require('mongoose')

// const User = mongoose.model('User')
const User = require('../../db/schema/user')
router.post('register', async ctx=>{  
	 const data = await User.find({email:ctx.request.body.email});
})
module.exports = router.routes()
```

==注释的部分，不知道为什么不能这样引入==

```
app.js

//引入路由
const users = require('./router/api/user')
 // 路由配置
  router.use('/api/users/',users)

```

### 获取用户传过来的数据 ctx.request.body

### 密码加密bcrypt

```
 await bcrypt.genSalt(10, function(err, salt) {
      bcrypt.hash(user.password, salt, function(err, hash) {
          if(err){
            throw err
          }
          console.log(hash)
          user.password = hash
      });
  });
```

必须明确一点：
 Bcrypt是单向Hash加密算法，类似Pbkdf2算法 不可反向破解生成明文。

一、Bcrypt是怎么加密的？

Bcrypt有四个变量：

1. saltRounds: 正数，代表hash杂凑次数，数值越高越安全，默认10次。
2. myPassword: 明文密码字符串。
3. salt: 盐，一个128bits随机字符串，22字符
4. myHash: 经过明文密码password和盐salt进行hash，个人的理解是默认10次下 ，循环加盐hash10次，得到myHash

每次明文字符串myPassword过来，就通过10次循环加盐salt加密后得到myHash, 然后拼接BCrypt版本号+salt盐+myHash等到最终的bcrypt密码 ，存入数据库中。
 这样同一个密码，每次登录都可以根据自省业务需要生成不同的myHash, myHash中包含了版本和salt，存入数据库。
 bcrypt密码图解：



![img](https:////upload-images.jianshu.io/upload_images/3111490-d12e59731291f022.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

bcrypt密码组成.png

如Spring Security crypto 项目中实现的BCrypt方法加密：`BCrypt.hashpw(myPassword, BCrypt.gensalt())`

那即使黑客得到了bcrypt密码，他也无法转换明文，因为之前说了bcrypt是`单向hash算法`；

二、那如果黑客使用彩虹表进行hash碰撞呢?
 有文章指出bcrypt一个密码出来的时间比较长，需要0.3秒，而MD5只需要一微秒（百万分之一秒），一个40秒可以穷举得到明文的MD5，在bcrypt需要12年，`时间成本太高`，这我就没仔细研究，详细可看陈皓的
 [如何防范密码被破解](https://coolshell.cn/articles/2078.html)

三、那他是如何验证密码的？
 在下次校验时，从myHash中取出salt，salt跟password进行hash；得到的结果跟保存在DB中的hash进行比对。
 如Spring Security crypto 项目中实现的BCrypt 密码验证`BCrypt.checkpw(candidatePassword, dbPassword)`

作者：martin6699

链接：https://www.jianshu.com/p/2b131bfc2f10

来源：简书

简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。

#### 解决异步加盐失败

即异步的加盐还没返回，数据就存在数据库了

使用同步

```
const bcrypt = require('bcryptjs')
const tools = {
  enbcrypt(password){
    var salt = bcrypt.genSaltSync(10);
    var hash = bcrypt.hashSync(password, salt);
    return hash
  }
}

module.exports = tools

```

在获取数据的时候就加盐

```
 const user = new User({
      name:ctx.request.body.name,
      email:ctx.request.body.email,
      password:tools.enbcrypt(ctx.request.body.password)
    })
```

### 登录接口

```
//登录路由
router.post('login', async ctx =>{
  const findResult = await User.find({email:ctx.request.body.email});
  const password = ctx.request.body.password;
  if(findResult.length === 0){
    ctx.status = 404;
    ctx.body = {email:'用户不存在'}
  }else{
    const data =  bcrypt.compareSync(password, findResult[0].password);
    if(data){
      ctx.status = 200;
      ctx.body = {msg:'登录成功'}
    }else{
      ctx.status = 400;
      ctx.body = {msg:'密码错误'}
    }
  }
})
```

### 生成token       jsonwebtoken

[JWT](<http://www.ruanyifeng.com/blog/2018/07/json_web_token-tutorial.html>)

```
var token = jwt.sign({ foo: 'bar' }, 'shhhhh');
```

这里的'shhhh'服务器自己保存的key值

  ```
const payload = {id:loginUser.id, name:loginUser.name, }
var token = jwt.sign(payload, 'secret',{expiresIn:3500});
  ```

### 获取token信息

#### 模块 passport-jwt koa-passport

```
app.js
const passport = require('koa-passport')
//初始化并进行监听
app.use(passport.initialize())
app.use(passport.session())
require('./config/passport')(passport)
```

passport.js

```
module.exports = passport =>{
//这里就可以收到app.js传入的passport的信息
    }
```

引入passport-jwt

```
var JwtStrategy = require('passport-jwt').Strategy,
    ExtractJwt = require('passport-jwt').ExtractJwt;
const keys = require('./keys')
const mongoose = require('mongoose')
const userModel = mongoose.model('User')
var opts = {}
opts.jwtFromRequest = ExtractJwt.fromAuthHeaderAsBearerToken();
opts.secretOrKey = keys.secretOrKey;
    module.exports = passport =>{
      passport.use(
        new JwtStrategy(opts, async (jwt_payload, done)=> {
          const user = await userModel.findById(jwt_payload.id)
          if(user){
            return done(null,user)
          }else{
            return done(null, false)
          }
    }));
}
```

在路由的user.js(也要引入passport)

```
router.get('current',passport.authenticate('jwt', { session: false }), ctx => {
    ctx.body = ctx.state.user
    // console.log(ctx)
})
module.exports = router.routes()
```



```
$ npm init -y
Wrote to D:\codeword\webpack\demo\package.json:

{
  "name": "demo",
  "version": "1.0.0",
  "description": "",
  "main": "demo.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}

```
注意是npm
```
$ npm install -D webpack webpack-cli
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN demo@1.0.0 No description
npm WARN demo@1.0.0 No repository field.
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@1.2.7 (node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.2.7: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})

+ webpack-cli@3.3.0
+ webpack@4.29.6
added 388 packages from 217 contributors and audited 5229 packages in 36.856s
found 0 vulnerabilities


```
因为没有全局配置webpack 所以需要找到webpack
```
$ node_modules/.bin/webpack demo.js --output dist/bundle.js
Hash: 37f534b756534ef5e25b
Version: webpack 4.29.6
Time: 503ms
Built at: 2019-03-21 21:54:39
    Asset       Size  Chunks             Chunk Names
bundle.js  956 bytes       0  [emitted]  main
Entrypoint main = bundle.js
[0] ./demo.js 27 bytes {0} [built]

WARNING in configuration
The 'mode' option has not been set, webpack will fallback to 'production' for this value. Set 'mode' option to 'development' or 'production' to enable defaults for each environment.
You can also set it to 'none' to disable any default behavior. Learn more: https://webpack.js.org/concepts/mode/

```
### 进行打包 npm run build
```

$ npm run build

> demo@1.0.0 build D:\codeword\webpack\demo
> webpack

Hash: 240f2e52eaa309df9757
Version: webpack 4.29.6
Time: 150ms
Built at: 2019-03-21 22:10:14
    Asset       Size  Chunks             Chunk Names
bundle.js  979 bytes       0  [emitted]  main
Entrypoint main = bundle.js
[0] ./index.js 71 bytes {0} [built]

WARNING in configuration
The 'mode' option has not been set, webpack will fallback to 'production' for this value. Set 'mode' option to 'development' or 'production' to enable defaults for each environment.
You can also set it to 'none' to disable any default behavior. Learn more: https://webpack.js.org/concepts/mode/

```
## 管理多页面入口
```
//配置路径
const path = require('path');
module.exports = {
    entry:{
           index: './src/index.js',
           user:'./src/user.js'
         },
    output : {
        filename:'[name]/bundle.js',
        path: path.resolve(__dirname,'dist')//当前的dist文件夹
    }
}
```

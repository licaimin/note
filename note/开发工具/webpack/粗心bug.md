```
const path = require('path');
module.exports = {
  entry:'./src/index.js',
  output: {
    filename:'boundle.js',
    path:path.resolve(__dirname,'dist')
  },
  module: {
    rules: [
      {
        test:'/\.css$/',
        use: [
          'style-loader',
          'css-loader'
        ]
      }

    ]
  }
}
```



```
ERROR in ./src/style.css 1:0
Module parse failed: Unexpected token (1:0)
You may need an appropriate loader to handle this file type.
> .hello {
|   color: red;
| }
 @ ./src/index.js 2:0-21
npm ERR! code ELIFECYCLE
npm ERR! errno 2
npm ERR! webpackStudy@1.0.0 build: `webpack`
npm ERR! Exit status 2
npm ERR!
npm ERR! Failed at the webpackStudy@1.0.0 build script.
npm ERR! This is probably not a problem with npm. There is likely additional logging output above.

npm ERR! A complete log of this run can be found in:
npm ERR!     C:\Users\Administrator\AppData\Roaming\npm-cache\_logs\2019-05-05T08_42_34_896Z-debug.log
```

==只是我的表达式写错了，写了正则匹配css,就不用加双引号==

` test:'/\.css$/',` 改为 ` test:/\.css$/,`

网上google答案

Please look at your regex for matching. It is wrong. It should be:

`/\.css$/` for css

`/\.js$/` for js

Your backslash is at the wrong position. Your regex matches files named: style\css
# webpack

**模块打包机,webpack是收把项目当作一个整体，通过一个给定的的主文件，webpack将从这个文件开始找到你的项目的所有依赖文件，使用loaders处理它们，最后打包成一个或多个浏览器可识别的js文件**

### Loader

1、实现对不同格式的文件的处理，比如说将scss转换为css，或者typescript转化为js
2、转换这些文件，从而使其能够被添加到依赖图中
loader是webpack最重要的部分之一，通过使用不同的Loader，我们能够调用外部的脚本或者工具，实现对不同格式文件的处理，loader需要在webpack.config.js里边单独用module进行配置，配置如下：

babel-loader： 让下一代的js文件转换成现代浏览器能够支持的JS文件。
babel有些复杂，所以大多数都会新建一个.babelrc进行配置
css-loader,style-loader:两个建议配合使用，用来解析css文件，能够解释@import,url()如果需要解析less就在后面加一个less-loader

```
module.exports = {
 
    entry: __dirname + '/src/main.js',
 
    output: {
        path: __dirname + '/output',
        filename: 'main.js'
    },
 
    module: {
        loaders: [
            {
                test: /\.css$/,
                loader: 'style-loaer!css-loader'
            }
       ]
    }
}
```

**css-loader style-loader**

因此，我们这样配置后，遇到后缀为.css的文件，webpack先用css-loader加载器去解析这个文件，遇到“==@import==”等语句就将相应样式文件引入（所以如果没有css-loader，就没法解析这类语句），最后计算完的css，将会使用style-loader生成一个内容为最终解析完的css代码的style标签，放到head标签里。

需要注意的是，loader是有顺序的，webpack肯定是先将所有css模块依赖解析完得到计算结果再创建style标签。因此应该把style-loader放在css-loader的前面（webpack loader的执行顺序是从右到左）。

### plugins

plugins和loader很容易搞混，说都是外部引用有什么区别呢？ 事实上他们是两个完全不同的东西。这么说**loaders负责的是处理源文件的如css、jsx，一次处理一个文件。而plugins并不是直接操作单个文件，**它直接对整个构建过程起作用

UglifyJsPlugin: 压缩代码
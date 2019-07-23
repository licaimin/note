https://github.com/tj/commander.js/blob/master/Readme_zh-CN.md

node.js命令行界面的完整解决方案

# [npm package.json属性详解](https://www.cnblogs.com/tzyy/p/5193811.html)

# bin

很多模块有一个或多个需要配置到PATH路径下的可执行模块，npm让这个工作变得十分简单（实际上npm本身也是通过bin属性安装为一个可执行命令的）
如果要用npm的这个功能，在package.json里边配置一个bin属性。bin属性是一个已命令名称为key，本地文件名称为value的map如下：

```
{ "bin" : { "myapp" : "./cli.js" } }
```

模块安装的时候，若是全局安装，则npm会为bin中配置的文件在bin目录下创建一个软连接（对于windows系统，默认会在C:\Users\username\AppData\Roaming\npm目录下），若是局部安装，则会在项目内的./node_modules/.bin/目录下创建一个软链接。
因此，按上面的例子，当你安装myapp的时候，npm就会为cli.js在/usr/local/bin/myapp路径创建一个软链接。
如果你的模块只有一个可执行文件，并且它的命令名称和模块名称一样，你可以只写一个字符串来代替上面那种配置，例如：

```
{ "name": "my-program"
, "version": "1.2.5"
, "bin": "./path/to/program" }
```

作用和如下写法相同:

```
{ "name": "my-program"
, "version": "1.2.5"
, "bin" : { "my-program" : "./path/to/program" } 
```

https://juejin.im/post/5ab3f77df265da2392364341

### 5.2 node_modules/.bin 目录

上面所说的 `node_modules/.bin` 目录，保存了依赖目录中所安装的可供调用的命令行包。

何谓命令行包？例如 `webpack` 就属于一个命令行包。如果我们在安装 webpack 时添加 `--global` 参数，就可以在终端直接输入 `webpack` 进行调用。但如果不加 `--global` 参数，我们会在 `node_modules/.bin` 目录里看到名为 webpack 的文件，如果在终端直接输入 `./node_modules/.bin/webpack` 命令，一样可以执行。

这是因为 `webpack` 在 `package.json` 文件中定义了 `bin` 字段为:

```
{
    "bin": {
        "webpack": "./bin/webpack.js"
    }
}
复制代码
```

bin 字段的配置格式为: `<command>: <file>`, 即 `命令名: 可执行文件`. npm 执行 install 时，会分析每个依赖包的 package.json 中的 `bin` 字段，并将其包含的条目安装到 `./node_modules/.bin` 目录中，文件名为 `<command>`。而如果是全局模式安装，则会在 npm 全局安装路径的 bin 目录下创建指向 `<file>` 名为 `<command>` 的软链。因此，`./node_modules/.bin/webpack` 文件在通过命令行调用时，实际上就是在执行 `node ./node_modules/.bin/webpack.js` 命令。

正如上一节所说，`npm run` 命令在执行时会把 `./node_modules/.bin` 加入到 `PATH` 中，使我们可直接调用所有提供了命令行调用接口的依赖包。所以这里就引出了一个最佳实践：


作者：rianma链接：https://juejin.im/post/5ab3f77df265da2392364341来源：掘金著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
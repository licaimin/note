# npm

npm（node package manager）node的包管理工具当一个网站依赖的代码越来越多，程序员发现这是一件很麻烦的事情：

去 jQuery 官网下载 jQuery 
去 BootStrap 官网下载 BootStrap 
去 Underscore 官网下载 Underscore 
……

有些程序员就受不鸟了，npm 给出一个解决方案：用一个工具把这些代码集中到一起来管理吧！

**NPM 的思路大概是这样的：**

- 买个服务器作为代码仓库（registry），在里面放所有需要被共享的代码
- 发邮件通知 jQuery、Bootstrap、Underscore 作者使用 npm publish 把代码提交到 registry
  上，分别取名 jquery、bootstrap 和 underscore（注意大小写）
- 社区里的其他人如果想使用这些代码，就把 jquery、bootstrap 和 underscore 写到 package.json
  里，然后运行 npm install ，npm 就会帮他们下载代码

```
        $ npm install jquery
```

- 下载完的代码出现在 node_modules 目录里，可以随意使用了。

  ##  npm 模块安装机制简介

  1. 安装之前，`npm install`会先检查，==`node_modules`==目录之中是否已经存在指定模块。如果存在，就不再重新安装了，即使远程仓库已经有了一个新版本，也是如此。

  如果你希望，一个模块不管是否安装过，npm 都要强制重新安装，可以使用==`-f`或`--force=`=参数。 	

2.  更新模块

   答案是 npm 模块仓库提供了一个查询服务，叫做 ==registry==。以 npmjs.org 为例，它的查询服务网址是 [`https://registry.npmjs.org/`](https://registry.npmjs.org/) 。

   这个网址后面跟上模块名，就会得到一个 JSON 对象，里面是该模块所有版本的信息。比如，访问 [`https://registry.npmjs.org/react`](https://registry.npmjs.org/react)，就会看到 react 模块所有版本的信息。

   它跟下面命令的效果是一样的。

   > ```bash
   > $ npm view react
   > 
   > # npm view 的别名
   > $ npm info react
   > $ npm show react
   > $ npm v react
   > ```

   registry 网址的模块名后面，还可以跟上版本号或者标签，用来查询某个具体版本的信息。比如， 访问 https://registry.npmjs.org/react/v0.14.6 ，就可以看到 React 的 0.14.6 版。

   返回的 JSON 对象里面，有一个`dist.tarball`属性，是该版本压缩包的网址。

   > ```javascript
   > dist: {
   >   shasum: '2a57c2cf8747b483759ad8de0fa47fb0c5cf5c6a',
   >   tarball: 'http://registry.npmjs.org/react/-/react-0.14.6.tgz' 
   > },
   > ```

   到这个网址下载压缩包，在本地解压，就得到了模块的源码。`npm install`和`npm update`命令，都是通过这种方式安装模块的。

==总结一下，Node模块的安装过程是这样的。==

> 1. 发出`npm install`命令
> 2. npm 向 registry 查询模块压缩包的网址
> 3. 下载压缩包，存放在`~/.npm`目录
> 4. 解压压缩包到当前项目的`node_modules`目录

注意，一个模块安装以后，本地其实保存了两份。一份是`~/.npm`目录下的压缩包，另一份是`node_modules`目录下解压后的代码。

但是，运行`npm install`的时候，只会检查`node_modules`目录，而不会检查`~/.npm`目录。也就是说，如果一个模块在`～/.npm`下有压缩包，但是没有安装在`node_modules`目录中，npm 依然会从远程仓库下载一次新的压缩包。

**离线下载**

区已经为npm的离线使用，提出了几种解决方案。它们可以大大加快模块安装的速度。

解决方案大致分成三类。

**第一类，Registry 代理。**

> - [npm-proxy-cache](https://www.npmjs.com/package/npm-proxy-cache)
> - [local-npm](https://github.com/nolanlawson/local-npm)（[用法](https://addyosmani.com/blog/using-npm-offline/)）
> - [npm-lazy](https://github.com/mixu/npm_lazy)

上面三个模块的用法很类似，都是在本机起一个 Registry 服务，所有`npm install`命令都要通过这个服务代理。

> ```bash
> # npm-proxy-cache
> $ npm --proxy http://localhost:8080 \
>   --https-proxy http://localhost:8080 \
>   --strict-ssl false \
>   install
> 
> # local-npm
> $ npm set registry http://127.0.0.1:5080
> 
> # npm-lazy
> $ npm --registry http://localhost:8080/ install socket.io
> ```

有了本机的Registry服务，就能完全实现缓存安装，可以实现离线使用。

**第二类，npm install替代。**

如果能够改变`npm install`的行为，就能实现缓存安装。[`npm-cache`](https://www.npmjs.com/package/npm-cache) 工具就是这个思路。凡是使用`npm install`的地方，都可以使用`npm-cache`替代。

> ```bash
> $ npm-cache install
> ```

**第三类，node_modules作为缓存目录。**

这个方案的思路是，不使用`.npm`缓存，而是使用项目的`node_modules`目录作为缓存。

> - [Freight](https://github.com/node-freight/freight)
> - [npmbox](https://github.com/arei/npmbox)

上面两个工具，都能将项目的`node_modules`目录打成一个压缩包，以后安装的时候，就从这个压缩包之中取出文件。
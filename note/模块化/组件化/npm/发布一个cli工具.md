[发布一个cli工具](https://zhuanlan.zhihu.com/p/38730825)

bin目录下的cli.js

==js 中首行设置如下代码：==不然会报错说找不到路径

```text
#!/usr/bin/env node
```

上面这句话是用来告诉操作系统用node来运行这个文件

可以在js中console.log('hello mei')

https://juejin.im/post/5af2a2cbf265da0b9c109f59



https://juejin.im/post/5b6b086cf265da0f8d368935

使用`process.argv`获取完整的输入信息

***commander***

我们选用 *nodejs* 的 *commander* 来制作 类似 *git* *docker* 风格的 *cli* 命令行工具 ， 因为没有其他更好的选择
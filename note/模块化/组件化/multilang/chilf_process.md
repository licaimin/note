[node.js](http://nodejs.cn/api/child_process.html)

# 1. chilf_process

```
const childProcess = require('child_process')
console.log(`开始执行 kxd-multilang create 命令 \n`)
childProcess.execSync(`cd ./example && node ../bin/cli.js create`, { shell: true, stdio: 'inherit' })
console.log(`\n kxd-multilang create 命令 执行完成 \n`)
```

```
child_process 模块提供了衍生子进程的能力（以一种与 popen(3) 类似但不相同的方式）。 此功能主要由 child_process.spawn() 函数提供：
```

# 2.chark

`chalk` 包的作用是修改控制台中字符串的样式，包括：

1. 字体样式(加粗、隐藏等)
2. 字体颜色
3. 背景颜色

### 2.1.使用

`chalk` 支持两种方式使用：常规的调用方式和模板中使用的方式。

### 2.2 常规使用

```
const chalk = require('chalk');
console.log(chalk.red.bold.bgWhite('Hello World'));
```

上面代码执行的结果是，`Hello World` 加粗，字体颜色是红色，背景颜色是白色。

> 注意：背景颜色要在 bg 后面加上具体的颜色，颜色的第一个字母大写。

# 3.update-pkg（对package.json内容的解析）

[关于这个npm](https://www.npmjs.com/package/update-pkg)

### .data

Type: `object`
Default: `{}`

The parsed content of `package.json`   (对package.json解析的内容)
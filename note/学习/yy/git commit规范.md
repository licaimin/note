组件化的规范化、自动化
1. 分支名的规范

随着组件库的组件的增多，在开发的过程中，需要将未完成的代码提交到 组件库 中去，这个时候新建一个分支就是一个比较好的选择

分支名规范的格式： [状态]-[开发者名|组件名]



状态
说明




dev
组件处于开发状态


...
...



2. commit 内容规范

组件库是一个公共项目，不能随便提交一些没有commit或者commit不明确的信息，所以commit内容的规范也十分重要

[type](组件名): 具体修改信息
type 说明



type
说明




feat
新功能（feature）


fix
修补bug


docs
文档


style
格式（不影响代码运行的变动）


refactor
重构（即不是新增功能，也不是修改bug的代码变动）


test
增加测试


chore
构建过程或辅助工具的变动



standard-version 接入

组件库引入 git hook 来进行提交前的预检测，检测开发者提交是否符合规范
规范了组件开发者的 commit，就可以使用 standard-version ，组件运行 npm run release 进行 chore 的过程，最后 push 到 origin

之所以规范了 commit ，不仅让提交的组件修改更清晰，通过 standard-version ，可以方便地自动化构建出 Changelog


Changelog 采用直接放在跟组件下，这个参考了 babel 的处理方式，因为我们提交都是直接整个 kxd-util 的提交，将 Changelog 放到每个组件下不好实现

3. 组件库的单元测试

规范每个组件的 package.json 的 script

js 组件使用 jest：范例 @yy/platform 和 @yy/cookie

vue 组件使用 Vue Test Utils vue组件介入测试用例感觉比写个组件还麻烦，这个待定哟

4. 自动化构建发布

实现代码 push 到 origin ，ci 自动运行修改组件的测试用例，构建，发布到 @yy/npm


开发者要做的事情就是，在最后整合到 master 的时候，版本号由组件管理者进行修改，参考修改 version 的相关规范，这样的好处一个是管理者可以自行控制，还有就是在ci改版本号同步不了到项目本身
使用 ci 进行构建发布组件，这样组件大家就都有权限可以更改，避免有时候要扩展这个组件却没有权限的窘境

版本规范
基本格式： [name].x.y.z-[state]



序号
说明




x
主版本号(major)，进行不向下兼容的修改时，递增主版本号


y
次版本号(minor)，保持向下兼容,新增特性时，递增次版本号


z
修订号(patch),保持向下兼容,修复问题但不影响特性时，递增修订号




tip：0.y.z 表示开发阶段，一切可能随时改变，非稳定版


1.0.0 界定此版本为初始稳定版，后面的一切更新都基于此版本进行修改




state
说明
含义




α或a
alpha 版
内测版本，内部测试的版本，bug 较多


β或b
beta 版
公测版本，给外部进行测试的版本，有缺陷


γ或g
Gamma 版
相当成熟的测试版，于发行版相差无几


rc
Release Candidate
是前面三种测试版的进一步版本，实现了全部功能，清除了大部分 bug，接近发布倒计时，有时会进一步细分为 rc1,rc2



// 获取修改的组件的脚本并写入一个文件
const childProcess = require('child_process')
const changeDiff = childProcess.execSync(`git diff origin/master...HEAD --name-only`, { shell: true })
const fs = require('fs')

const changeFile = changeDiff.toString().match(/packages\/(.+)\//g)
let changePkgList = []

if (changeFile) {
  changeFile.forEach(v => {
    if (changePkgList.indexOf(v) === -1) {
      changePkgList.push(v)
    }
  })
  try {
    fs.writeFileSync('./scripts/change-pkg', changePkgList.join('&&'))
    childProcess.execSync(`git add ./scripts/change-pkg"`, { shell: true, stdio: 'inherit' })
    childProcess.execSync(`git commit -m "style(kxd_util): commit change-pkg file"`, { shell: true, stdio: 'inherit' })
  } catch (e) {
    console.log(e)
  }
  console.log('changePkgList', changePkgList)
} else {
  process.exit(1)
}


// ci 工具脚本
const childProcess = require('child_process')
const lastChangeLog = childProcess.execSync(`git log --pretty=format:"%s" -1`, { shell: true })
const fs = require('fs')
let changePkgList
const command = [
  'npm i',
  'npm run test',
  'npm run build',
  'npm publish --registry=https://npm-registry.yy.com'
]

// 最后一次不是 不是 chore(release) 啥也不做
if (lastChangeLog && !/chore/.test(lastChangeLog.toString())) {
  console.log('没有运行 npm run release')
  return
}

try {
  changePkgList = fs.readFileSync('./scripts/change-pkg').toString().split('&&')
} catch (e) {
  console.log('找不到修改文件 change-pkg 自动化构建发布失败')
  process.exit(1)
}

if (changePkgList.length) {
  changePkgList.forEach((pkgPath, index) => {
    // 2-cd到对应的目录下，进行单元测试
    console.log(`开始处理第个 ${index + 1} 组件 ${pkgPath.replace(/packages/, '')} 路径为 ${pkgPath}`)
    console.log(`cd ./${pkgPath}`)
    command.forEach(v => {
      console.log(v)
    })
    childProcess.execSync(`cd ./${pkgPath} && ${command.join('&&')}`, { shell: true, stdio: 'inherit' })
    console.log('发布完成')
  })
} else {
  console.log('没有修改组件')
}

// 整个 ci yarm 没啥，只是运行了 npm run ci 就是执行上面的 js
// 难点在于怎么实现在 ci 发布组件
// 在 ci runner 无法输入 npm 用户名密码这些东西
// npm login 不支持后面的参数带上用户名和密码，但是登录成功的用户本地有 .npmrc 这个用户文件
// 在 runner 服务器将这个 .npmrc 文件再 docker build 的时候放到 docker image 里面，让启动的 docker 有 npm 的登录环境
// 最后，需要大家的包都 添加这个公共的用户即可
// npm owner add xxx @yy/ccc --registry=https://npm-registry.yy.com
autokxdutil:
  stage: deploy
  tags:
    - auto-kxd-util
  before_script:
    - npm i --production
  script:
    - npm run ci
  cache:
    paths:
      - node_modules/
  only:
    - dev_kxd-util
FROM node

SHELL ["/bin/bash", "-c"]

RUN 'export SASS_BINARY_SITE=https://npm.taobao.org/mirrors/node-sass/' >> ~/.bashrc \
    && echo 'export PHANTOMJS_CDNURL=https://npm.taobao.org/mirrors/phantomjs/' >> ~/.bashrc \
    && echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc \
    && . ~/.bashrc \
    && cp /usr/share/zoneinfo/Hongkong /etc/localtime \
    && yarn config set registry https://npm-registry.yy.com \
    && npm config set registry https://npm-registry.yy.com \    
    && mkdir ~/.npm-global \
    && npm config set prefix '~/.npm-global' \
    && npm i -g babel-core@^6.26.3 \
    && npm i -g babel-eslint@^8.2.6 \


COPY .npmrc /root/


踩坑：git.yypm.com 这个域名的真实 ip 和在 runner 内 ping 的 ip 不一致但是可通，在 注册 runner 的时候要本地配置 host 指向真实的 ip

5. 组件库性能测试方案


使用手机测试：使用 webpack 为基本构建工具，将页面 build 好的页面生成一张二维码指向构建好的链接，准备一步性能较差的手机，扫码并自动运行 500 次 组件加载到运行的时间并取时间平均数作为最后的性能数据

使用node进行测试：完全脱离页面进行 js组件的性能测试，在 ci 启动一个限制了 内存 及 cpu 的 docker 容器，在这样的环境下进行性能测试虽然不能模拟正常的用户行为，但是可以做到自动化的性能测试

手机测试
// Measure 测试类 使用 ES5 的方法就不用引入一些 polyfill
function Measure (opt) {
  this.option = opt
  this.queue = []
}

// 只是用来最后生成报告的
Measure.prototype.report = function () {
  var average = function (arr) {
    return arr.reduce(function (acc, val) { return acc + val }, 0) / arr.length
  }
  var wrap = document.createElement('div')
  var report = '<h2>' + this.option.name + ' 性能测试报告 </h2>' +
  '<i> 运行次数为 ' + this.option.times + '</i>'
  this.queue.forEach(function (item) {
    report += '<p><strong>' + item.name + '</strong>的平均执行时间为： ' + average(item.data) + ' ms </p>'
  })
  wrap.innerHTML = report
  var firstChild = document.body.children[0]
  if (firstChild) {
    document.body.insertBefore(wrap, firstChild)
  } else {
    document.body.appendChild(wrap)
  }
}

// 用于真实的测试
Measure.prototype.test = function () {
  for (var i = 0; i < this.option.times; i++) {
    this.queue.forEach(function (item) {
      var start = window.performance.now()
      item.fn()
      var end = window.performance.now()
      var diff = end - start
      console.log('性能测试功能 ' + item.name + ' 执行时间: ', diff, 'ms')
      item.data.push(diff)
    })
  }
  this.report()
}

// 用于把放在 next 的函数放到队列中按顺序执行
Measure.prototype.next = function (name, fn) {
  this.queue.push({
    name: name,
    fn: fn,
    data: []
  })
  if (this.queue.length === this.option.count) {
    this.test()
  } else {
    return this
  }
}

import { setCookie, getCookie, delCookie } from '../lib/cookie'

new Measure({
  name: 'cookie component', // 组件名称
  times: 200, // 运行次数
  count: 4 // 需要测试的功能数量，和next数一致
}).next('组件加载', function () {
  // 这样写可能会内存泄露
  require('../lib/cookie')
}).next('setCookie', function () {
  setCookie('name', 'test')
}).next('getCookie', function () {
  getCookie('name')
}).next('delCookie', function () {
  delCookie('name')
})

// 挖出来 ipv4 的 ip ，用于启动，可以让外部手机通过这个 ip 扫码
const qrcode = require('qrcode-terminal')

const port = Math.floor(Math.random() * 1000 + 1000)
let IPAddress = '0.0.0.0'
for (var devName in interfaces) {
  const iface = interfaces[devName]
  for (let i = 0; i < iface.length; i++) {
    const alias = iface[i]
    if (alias.family === 'IPv4' && alias.address !== '127.0.0.1' && !alias.internal) {
      IPAddress = alias.address
    }
  }
}
const tgUrl = `http://${IPAddress}:${port}`

// 用这个库来生成一张二维码
qrcode.generate(tgUrl, {small: true}, (qrcode) => {
  console.log('调试地址为 ', tgUrl)
  console.log(qrcode)
})



todo


vue 的组件测试 vue-test-utils


不使用脚本测试工具了，而使用类似 iview 组件的形式，把组件放在组件聚合页中实际操作

vue 组件的性能测试

借助 npc ，在 vue 组件的钩子中展示性能数据

参考资料

版本命名及限定规则详解
ip 关系
vue 单元测试
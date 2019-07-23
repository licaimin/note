[npm 包的更新](https://www.cnblogs.com/penghuwan/p/6973702.html)

[更新npm包详细](https://juejin.im/post/5c5012926fb9a049d37f81e1)

#### npm 包的发布流程

> 本文主要是针对 还未曾发布过自己的 npm 的同学，阐述一下 npm 的发布流程
> 熟悉的同学，可以绕道了。

1. 首先你得有一个 自己的 npmjs.com 的账号 （没有的话，就到 npmjs.com 上去注册一个）

2. 然后 在需要发布的文件的 文件夹下 打开 cmd 并 npm init 生成 package.json 文件

3. 接下来 按照 node 的提示步骤走完。

4. 然后 在当前 cmd 中 输入 npm adduser， 然后按照提示输入 用户名 和 密码 以及 你注册账号时候的 邮箱

5. 然后 检测一下 是否登录成功 npm whoami

6. 提示你 已经 登录了， 就表示已成功登录

7. 最后一步就是 npm publish

     ```
   可是系统总是提示 You do not have permission to publish "test-npm-module". Are you logged in as the correct user? : test-npm-module
   ```

   ```
   此问题我已经解决了。
   
   之所以会出现这种问题，根本原因还是因为包名与线上的包名冲突了。
   
   只需要修改包名，保证包名不与他人包名冲突即可。
   
   但是这条错误信息的提示却并没有体现出包名冲突这一信息，因此导致了
   
   我长时间不得其解。
   ```

   


## 第一步

新建项目要在组里面新建。建个人的话，到时无法发布

导航栏的groups->webs -> ikaixindou



命名格式`bossms-201704-feat-pc`

![img](file:///E:/%E5%BE%AE%E4%BF%A1%E6%96%87%E4%BB%B6/note/qqDC201BC4BC969A93C2B18387ED86A4E8/a1f20e3fda6e4afeae4518340e18beb5/e4e05f81f6e54a3c94e010b9efdcc5e4.jpg)

注意不要有空格

然后git拷贝项目

然后复制一个项目的文件夹（这里用的是斋日活动的）

### 开始修改啦

这个是修改dragout发布

```
"dragont": {
    "preset": "feteam",
    "biz": "kaixindou",
    "domain": "kxd.yy.com"
  }
```

注意路径一定要独一无二

才能发

然后再改下

readme的链接，看下图

![img](file:///E:/%E5%BE%AE%E4%BF%A1%E6%96%87%E4%BB%B6/note/qqDC201BC4BC969A93C2B18387ED86A4E8/913e0d3c619344548d9f1adc07feff88/25952d5789ed4229a1d9f0ba90c12d8e.jpg)

readme只保留以下信息

国内是 kxd(开心斗)

国外是()ihago

![img](E:/%E5%BE%AE%E4%BF%A1%E6%96%87%E4%BB%B6/note/qqDC201BC4BC969A93C2B18387ED86A4E8/7d18245112234b5abca97cd1f3008198/9024ccad4a604046aab0558c77a49c35.jpg)

### 现在是修改`fec.config.js文件`

#### 一其实fec是公司对webpack的封装

注意修改

>  master 为正式环境

所以链接为 

`https://kxd.yy.com`

>  测试的话就加个`test`

```
// 判断 CI 环境
  if (process.env.GITLAB_CI) {
    const cdnBase =
      process.env.CI_COMMIT_REF_NAME === 'master'
        ? 'https://kxd.yy.com' // master 分支为正式环境
        : 'https://kxd-test.yy.com' // 否则不使用CDN域名, 使用当前域名
    const cdnDir = 'a'
    const cdnProj = 'hago-statistics'
    path = `${cdnBase}/${cdnDir}/${cdnProj}`
  }
  
```

> 上图的` const cdnProj = 'hago-statistics'`这里的hago--statistics是我们在packge.json里的路径

![img](file:///E:/%E5%BE%AE%E4%BF%A1%E6%96%87%E4%BB%B6/note/qqDC201BC4BC969A93C2B18387ED86A4E8/64586c7e78bb48939c02b68507deb347/d0d6973e14b4447b9a657996a52b9f2c.jpg)

#### 二 注释掉

```
  // 离线缓存
      //  if (process.env.GITLAB_CI) {
      //   config
      //     .plugin('HagoManifestWebpackPlugin')
      //     .use(HagoManifestWebpackPlugin, [{
      //       passFepCheck: true,
      //       name: 'collect-card'
      //     }])
      // }
```

### 三注释.gitlab-ci.yml配置

![img](file:///E:/%E5%BE%AE%E4%BF%A1%E6%96%87%E4%BB%B6/note/qqDC201BC4BC969A93C2B18387ED86A4E8/88d82f9673454bc488da9f1a803d52a2/e701ddc517354cdf8dcfac5d9558504a.jpg)

```
.deploy_template: &deploy_definition
  stage: deploy
  tags:
    - fec
  variables:
    GIT_STRATEGY: none
  before_script:
    - npm i -g @yy/dragont-preset-feteam #发布
    #- npm i -g @yy/deployer #离线缓存
    # - npm i -g @yy/easy-sentry-cli @yy/easy-sentry-preset-feteam #错误收集
  script:
    # - easy-sentry release --preset feteam
    - dragont ci
    # - deployer notify
```



### fec相关配置的网站

http://fec.yypm.io/#/configuration?id=postcss>

### npm i 

### npm run dev

### commit和push上去仓库

### 查看build跟deploy有没有错误

![img](file:///E:/%E5%BE%AE%E4%BF%A1%E6%96%87%E4%BB%B6/note/qqDC201BC4BC969A93C2B18387ED86A4E8/01e8d8962e69464da8c607ada38de08b/d977c873f349443bbcdce7611b47861d.jpg)

![img](file:///E:/%E5%BE%AE%E4%BF%A1%E6%96%87%E4%BB%B6/note/qqDC201BC4BC969A93C2B18387ED86A4E8/f2b76ca35cb94d9a835bc0813ab32a95/06306dfa316642f2b0c3b735f1f1ca63.jpg)

![img](file:///E:/%E5%BE%AE%E4%BF%A1%E6%96%87%E4%BB%B6/note/qqDC201BC4BC969A93C2B18387ED86A4E8/b4ab39af5e20441a95a083e229f70e55/d471e0ca933f46ababfa7deeb4480dc5.jpg)

![img](file:///E:/%E5%BE%AE%E4%BF%A1%E6%96%87%E4%BB%B6/note/qqDC201BC4BC969A93C2B18387ED86A4E8/3150093f8ddd4e6cb9b119df5c3e669d/091df76cf6f94e459078fd18c70df040.jpg)
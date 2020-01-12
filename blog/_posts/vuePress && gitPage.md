---
  date: 2020-01-07
  tag:
    - vuePress
  auth: xya370
  classification: daliy
---

# 利用vuePress搭建一个自己的在线博客（一）
  作为一个码农，搭建自己的博客都快是基础技能了，作为一个没有云服务的人，我也想要拥有一个自己的博客，记录一下哪些年遇到的坑。经过仔细研究，vuePress,就决定是你了。

## vuePress的简单介绍以及快速使用：

### 介绍
   [官方文档：](https://www.vuepress.cn/plugin/)

   vuePress 是一个简单的极简静态网站生成，具有自己的主题和插件API，主要编码语言是使用markdown进行文档编辑，同时也可以使用vue生成自己的组件。事实上，vuePress是一个由vue, Vue Router, webpack驱动的单页面开发

### 快速上手：
  默认已有node环境，会简单使用npm

      全局安装：  npm install vuepress -g
      新建文件夹 myBlog
      cd myBlog
      npm install create-vuepress
      npm create vuepress
        输出：
        select the boilerplate type(Use arr keys) 选择需要的项目类型
          docs - Create a doucmentation project with VuePress
          blog - Create a blog with VuePress
        项目创建之后：
      npm install
      npm run dev
  默认端口号： 8080, 然后浏览器打开localhost:8080，就可以看到本地的blog项目了。

### 简单的配置介绍：
  之前，我们已经新建了一个vuepress-blog项目。
  简单介绍下目录：

    |-- my_blogs
      |-- blog
      |   |-- .vuepress
      |   |   |-- components //里面的组件被注册为全局组件，可在任意地方使用
      |   |   |-- styles
      |   |   |-- config.js // 基本配置文件
      |   |   |-- enhanceApp.js
      |   |-- _posts // 用于保存blog文件
      |-- .gitignore
      |-- package-lock.json
      |-- package.json
      |-- README.md

  下面重点了解下.vuepress下的config.js,这里面保存着基本配置，我们可以在里面简单的修改相关配置，至于如何修改主题，这个等待后期填坑。

## 部署
  我们现在已经可以在本地查看blog了，但是我们要怎样部署到gitPage上，让更多人可以通过链接来查看呢。<br>
  具体参考： [部署：](https://www.vuepress.cn/guide/deploy.html)

  1. 在远程github上新建一个项目
  2. 在package.json同级目录下，新建一个deploy.sh文件
    内容：
      ```
        #!/usr/bin/env sh
        # 确保脚本抛出遇到的错误
        set -e
        # 生成静态文件 //根据本地实际情况修改
        npm run build
        # 进入生成的文件夹
        cd blog/.vuepress/dist

        # 如果是发布到自定义域名
        # echo 'www.example.com' > CNAME

        git init
        git add -A
        git commit -m 'deploy'

        # 如果发布到 https://<USERNAME>.github.io
        # git push -f git@github.com:<USERNAME>/<USERNAME>.github.io.git master

        # 如果发布到 https://<USERNAME>.github.io/<REPO>
        # htts:xxxx.github.io/project
        # git push -f git@github.com:<USERNAME>/<REPO>.git master:gh-pages

        cd -
      ```
  3. 然后可以配置package.json:

      script: {
        "deploy": "bash deploy.sh"
      }
  4. npm run deploy 等待执行完成，就部署成功了

### 部署踩坑一二

#### 关于deploy.sh文件
  如果报错：bash不是可执行的命令 <br>
  解决方法：
    1. 进入.sh文件所在目录，右键打开 git bash 输入bash deploy.sh
    2. 直接双击.sh文件

#### 关于github部署不上
  确认流程无误，只是最后部署不上，检查git执行是否有报错
  1. git 提交时报错信息：<br>
    ssh: connect to host github.com port 22: Connection timed out fatal: Could not read from remote repository.<br>
    解决： 这个就比较明确了，我们需要在本地添加一个ssh,并对github进行配置，
    至于这个ssh的配置，网上有许多文章进行说明，这里就不再说了。
  2. 关于文件上传后的页面查看：<br>
    github上本项目仓库 Settings -> GitHub Pages -> source 切换为git-pages brach 分支，获取页面链接


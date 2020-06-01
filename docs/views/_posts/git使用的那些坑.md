---
  title: git使用的那些常见坑
  date: 2020-05-12
  tags:
      - git
  auth: xya370
  categories:
    - 踩坑日常
---
:::tip
记录下使用git的一些小坑，防止每次都要查
:::

### 关于ssh

 1. git push read from socket failed connection reset by peer fatal could not read
    这是关于ssh的问题;
    * 首先尝试连接github.com ssh -T git@github.com
    * 如果连接正常，git config remote.origin.url git@github.com:your_username/your_project.git
    * 连接失败，重新设置ssh
        - 首先，git-ssh 相关文件放在用户名/.ssh目录下，清除id_rsa,id_rsa.pub文件中的内容，或删除这两个文件。
        - 使用该条命令 ssh-keygen -t rsa -C "you email" 重新生成ssh
        - 在打开你的github地址，setting - ssh and GPG keys - new SSH key 新增一个ssh,将id_rsa.pub中的内容复制粘贴在上面

### 关于git更新或下载的一些事
1. npm ERR! Unexpected end of JSON input while parsing near
 每次删除node_modules后重新进行npm install 会有一定几率报错，考虑同时删除package-lock.json 然后使用 npm cache clean --force 强制清除缓存然后重新 npm install

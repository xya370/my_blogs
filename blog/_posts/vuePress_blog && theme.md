---
    date: 2020-01-08
    tag:
        - vuePress
        - vuePress-theme
    auth: xya370
    classification: 挖坑中
---

# 利用vuePress搭建一个自己的在线博客（二）之主题修改和自定义
  在上一章中，我们已经成功的搭建了自己的博客，并部署好了，那么我们就来调整一下自己博客的主题吧，当然我们也可以使用其他已经设定好的主题，可以参考文档
  [文档：](https://www.vuepress.cn/theme/)
  当然我们这里主要是讲的是如何进行自定义主题

## 基础主题修改
  我们在之前新建博客时就已经下载了基础主题了，我们可以在这个主题上进行修改。

    .vuepress目录下新建theme文件夹
    将node_modules/@vuepress/theme-blog文件中的内容复制到新建的theme文件夹中
    此处可以在config.js中查看对应的主题进行主题文件的获取

  获取了主题文件后，我们就可以对其修改成我们所需要的样式了

### 主题文件目录一览

    |-- theme
        |-- components
        |-- fonts
        |-- global-components //此处组件注册为全局组件
        |-- layouts //主要布局文件
        |   |-- Layout.vue 这是默认的布局设置
        |   |-- GlobalLayout.vue 这个是使用的布局设置，可以在此处进行布局修改
        |-- styles
        |-- index.js
        |-- package.json
  一般来说，我们如果需要将自定义的layout改成默认的化，可以在GlobalLayout.vue中进行引用，也可以在单独的文章frontmatters中进行layout设置，不过本人是设置为了默认全局，这个以个人情况为准。了解这些之后，我们就可以新建一个mylayout.vue页面进行编写了。

## 编写进行中
  关于代码的具体编写以及其他，这里就不多做赘述了，这里说一下，如果我们需要在每个文章frontmatters中获取一些自定义的信息，需要怎么做
  如果我们在frontmatters中新增了一个classification类别，那么我们如何在组件中获取到对应的文章信息呢？

    theme/index.js中forntmatters内添加对应信息
      frontmatters： [
        {
          id: 'classification',
          keys: ['classification'],
          path: '/classification/', //对应路径
          frontmatter: { title: 'classification'}, //对应的frontmatter中的对象名
        }
      ]
  添加完后，我们可以打开浏览器的vuetool进行查看this.$root.$classification并获取了

  如果需要想默认主题中的nav一样拥有一个统一的跳转再进行选择，那么可以在.vuepress/config.js的themeConfig中进行对象添加，毕竟themeConfigz中的内容都可以使用$themeConfig进行获取。

  ok相关的内容就介绍到这里了，推荐大家在进行自定义主题修改时都打开vuetool进行查看，会有个比较直观的了解。
---
  title: 利用scss进行主题切换
  date: 2020-05-21
  tags:
      - css
      - scss
  auth: xya370
  categories:
    - 挖坑中
---
:::tip
基本思想：在html上添加data-theme属性通过对应的属性值切换不同的配置，使用data-theme时
:::

关于主题切换的这些事，网上已经有很多文章记录了，基本想法都是差不多的，在此，仅记录我自己使用的方法。设置的主题有三种:1.默认即白天主题 2.暗色主题 3.自定义主题，即自己选择颜色，我们在这先实现主题的切换。

## theme 主题切换
### config配置调整
使用theme.scss文件，需要调整config配置

```
修改后的配置
{
  test: sassRegex,
  exclude: sassModuleRegex,
  use: getStyleLoaders(
    {
      importLoaders: 3,
      sourceMap: isEnvProduction && shouldUseSourceMap,
    },
    'sass-loader'
  ).concat(
    {
      loader: "sass-resources-loader",
      options: {
        resources: [
            // 相关的主题文件
          path.resolve(__dirname, '../src/theme.scss'),
          path.resolve(__dirname, '../src/handler_theme.scss')
        ]
      }
    }
  ),
  // Don't consider CSS imports dead code even if the
  // containing package claims to have no side effects.
  // Remove this when webpack adds a warning or an error for this.
  // See https://github.com/webpack/webpack/issues/6571
  sideEffects: true,
},

```

### 配置theme.scss
默认项目已支持scss编译。在文件目录下新建一个theme.scss文件，保存theme配置
``` scss
    $normal:(
        // 属性名
        color: (
            // 相关类名： 属性值
            common: #454545,
            active: #FDD303,
          ),
        background-color: (
            common: #fff;
        )
      );
    $dark:(
        color: (
            common: #fff,
            active: #007aff
        ),
        background-color: (
            common: #000;
        )
    );
    $themes: (
        "default": $normal,
        "dark": $dark
    )
```
以上，基本设定好了其中两个主题，然后需要利用scss的mixin来使用。

### halder_themes.scss 进行后期处理
利用@mixin 来对themes配置进行处理，使其输出对应的内容，theme.scss同目录下新增halder_themes.scss
```scss
    @import './theme.scss'; //引入主题配置文件
    //遍历主题map
    @mixin themeify1 {
      @each $theme-name, $theme-map in $themes {
        //!global 把局部变量强升为全局变量
        $theme-map: $theme-map !global;
        //判断html的data-theme的属性值  #{}是sass的插值表达式
        //& sass嵌套里的父容器标识   @content是混合器插槽，像vue的slot
        [data-theme="#{$theme-name}"] & {
          @content;
        }
      }
    }
    //获取对应主题对应属性下相关状态的属性值
    @function isStyle($style, $type) {
        //map-get 根据对应的key值返回map中对应的值
        //map-get($map, $key)
      @return map-get(map-get($theme-map, $style), $type)
    }

    @mixin styles ($style,$type) {
      @include themeify1 {
        #{$style}:isStyle($style, $type);
      }
    }
```
### 使用
在scss文件中使用
```scss
    .font {
       @include styles("color","active")
    }
```
这时候可以看到，编译之后的css是这样的
```css
    [data-theme="default"] .font {
        color: #FDD303;
    }
```
对于不同的主题切换，我们只需要使用最简单的方法改变html中data-theme的属性值就可以切换啦

## 关于自定义主题
一般来说，许多自定义主题是提供几种不同色值，来进行主题设置，通常这种情况下是配置了不同色值的主题文件，但是，这不是我所设想的自定义主题，自定义主题，当然是可以随机选中一种颜色来进行配置都是可以的，甚至可以从图片中选取主题色来进行配置，关于如何设置色值的问题，在网上查了好久，最终发现css的var方法可以使用。
在theme.scss中新增部分内容
``` scss
    :root { // root选择器，匹配文档根元素，一般html文档中是html元素
      --defineColor: #FDD303; // 定义一个defineColor属性
    }

    $define: (
        color: (
            common: #454545,
            active: var(--defineColor) //var()用于插入自定义的属性值
        ),
        background-color: (
            common: #fff;
        )
    )
    $themes: (
        "define": $define
    )
```
对于使用方法无须修改，可以在文件中通过以下语句进行颜色修改

```javascript
    document.querySelector(':root').setAttribute('style', '--defineColor: '+color)
```
这样一个关于scss下的主题修改就好了，不过感觉还有挺多问题的，毕竟没有后端，无法保存主题，每次刷新都会回到初始状态，就先这样吧
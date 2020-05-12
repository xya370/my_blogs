---
  title: 那些我用过或没用过的css属性（踩坑持续更新中）
  date: 2020-01-19
  tags:
      - css
  auth: xya370
  categories:
    - 踩坑日常
---

<!-- # 那些我用过或没用过的css属性（踩坑持续更新中） -->

## css之镂空字体
  有时候，ui小姐姐设计的图是镂空字体，这就让人很头疼了，而大部分的时间内，这种字都是使用图片代替的，要进行改动又很麻烦，但是css中意外发现了属性可以实现哟，记录一下：

    html：
      <p> Hello, world</p>

    css:
      p {
        -webkit-text-stroke: 1px #fff; //文字描边
        -webkit-text-fill-color: transparent;
      }
    以上两个属性配套使用，即可生成镂空文字。

## 关于两个行内元素未对齐的问题
  遇到了个问题，在一排行内元素排列时，发现了未对齐的问题，还以为是属性哪里影响了，检查了很久，真的是百思不得其解。属性如下

    div
      display: inline-flex;

  本来是使用inline-block的，后来发现了这个问题，遂改成inline-flex,但是该属性在pc上可以实现，在手机上测不行。
  网上搜了一下，说问题出在没有对准基线上，修改如下

    div
      display: inline-block;
      vertical-align: middle;

## css换行的那些事
  1. 单行文本溢出，省略号显示

    div
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;

  2. 多行文本换行显示

    div
        display: -webkit-box;
        -webkit-line-clamp: 2;
        -webkit-box-orient: vertical;
        overflow: hidden;

    注： -webkit-line-clamp: 用来设置一个块级元素内部文本显示行数，目前支持-webkit-内核，需要考虑兼容性问题，同时需要配合-webkit-其他私有属性进行配合

## 关于ios:input以及flex的那些事：

### input高度问题：
  很神奇的事情，在页面中使用了未设立高度，通过padding对元素高度进行支撑，在安卓手机以及pc端支持良好，但是在ios中却发现input没有了高度，百思不得其解,而后进行解决，对其使用rem对高度进行设置，然而并不是很好用,经过网上查询，需要使用line-height对行高进行设置，从而解决input的设置高度无效问题;

### ios display:flex 高度塌陷
  在父元素上设置了flex,未设置高度，高度由子元素进行填充，在安卓上展示良好，在ios则是出现了高度塌陷的问题，之前在另一个页面上也运用了，但是没有出现问题，
  例子如下：

    .parent {
      padding: 1.25rem;
      display: flex;
      justify-content: space-between;
    }
    .child {
      width: 43%;
      text-align: center;
      padding: 1.25rem 0;
      border-radius: 8px;
      color: #fff;
      font-size: 1.4rem;
      line-height: 2rem;
    }

  关于这个问题，网上似乎比较少，但好歹还是找到了，其中有这么一个属性：flex-shrink;
  flex-shrink: 用来指明flex元素的收缩规则，当flex盒子空间不足时进行收缩，默认值为1,以对应的flex-shrink值进行收缩，负值无效，而当flex-shrink为0的时候则是不进行收缩，了解一下关于flex-shrink的计算方法：
    计算：

      .parent {
        width: 300px;
        display: flex;
        padding: 15px;
      }
      .child1 {
        width: 60%;
        flex-shrink: 1;
      }
      .child2 {
        width: 60%;
        flex-shrink: 2;
      }
      此时，两个子元素的宽度和超出父元素，产生收缩，基本：flexType - flexShrink * flexbase;
      需要明确一点就是，计算flex父元素收缩时内容是不包括padding,marging,border等部分的。
      即当前需要收缩的内容：flexContent = 270*20% = 54;
      基础收缩量： 54 / (1+2) = 18;
      .child1 : 270*0.6 - 18
      .child2: (270*.6) - (18*2)
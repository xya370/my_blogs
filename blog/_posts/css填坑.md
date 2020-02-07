---
  date: 2020-01-19
  tag:
      - css
  auth: xya370
  classification: 踩坑日常
---

# 那些我用过或没用过的css属性（踩坑持续更新中）

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
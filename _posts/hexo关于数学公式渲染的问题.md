---
title: hexo关于数学公式渲染的问题
date: 2018-10-02 17:37:20
tags:
---

非常遗憾地把主题从apollo换成了Daily，本人其实更喜欢apollo那种简洁感。至于为何要换，归根结底是因为突然发现hexo的jade渲染器与mathjax渲染器起了冲突，导致只要有mathjax出现的页面，数学公式完全出不来。毕竟，以后应该会写不少跟数据相关的东西，数学肯定是必不可少。昨天花了一晚上总算是又折腾好了。下面来讲一下最简洁的一个教程。
<!--more-->

## 操作之前

请先检查一下自己目前在用的主题是用啥写的，如果是jade写的话，建议更换，毕竟我就没有把Apollo的折腾好（明明之前没问题的，不知是手贱更新还是咋了）。如果求稳的话，建议换成用ejs写的主题，毕竟这是官方教程用的。那些号称专门用于hexo渲染的工具如果这种最主要的功能要起冲突的话，那就真是没辙了。

## 开始操作

目前我只尝试过安装过hexo-math以及hexo-renderer-mathjax这两个插件，就本人的实践效果看，后者比较好。首先，按照网上其他教程说的，先要进行如下操作。

```bash
npm uninstall hexo-renderer-marked --save
npm install hexo-renderer-kramed --save
```

>这是因为hexo自带的marked并不支持渲染数学公式。

好吧，我不是很懂其中机制，但顺着来确实成功了。而且连其中的格式化规则也不用改。

安装hexo-renderer-mathjax插件

```bash
npm install hexo-renderer-mathjax --save
```

来自2019年3月17日的更新

再完成hexo-renderer-mathjax的安装后，需要进入blog/node_modules/hexo-renderer-mathjax/mathjax.html文件，修改其中的script标签

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.1/MathJax.js?config=TeX-MML-AM_CHTML"></script>
```

然后在Daily，landscape等主题就可以直接开始写数学公式了。

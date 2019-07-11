---
title: CSS - position详解
date: 2019-03-24 16:39:15
tags:
---

万万没有想到，上一次今日头条的前端面试会挂在这个问题上，那么重新复习一下相关的基础知识吧。
<!--more-->

关于position有何作用，以下为MDN关于position的解释，

>CSS position属性用于指定一个元素在文档中的定位方式。top，right，bottom 和 left 属性则决定了该元素的最终位置。

文档流

以下摘自MDN关于文档流的布局方式说明

>正常的布局流（在布局介绍文章中提到）是将元素放置在浏览器视口内的系统。默认情况下，块级元素在视口中垂直布局——每个都将显示在上一个元素下面的新行上，并且它们的外边距将分隔开它们。
>内联元素表现不一样——它们不会出现在新行上；相反，它们互相之间以及任何相邻（或被包裹）的文本内容位于同一行上，只要在父块级元素的宽度内有空间可以这样做。如果没有空间，那么溢流的文本或元素将向下移动到新行。

取值类型

- static：
CSS对于位置的默认值，且无法通过top, right, bottom, left等属性改变当前节点的位置

- relative:
将position设置为relative后，将可以通过top, right, bottom, left等属性改变当前节点的位置，改变的起点是position:static的位置。换句话说，它是相对于static的位置进行位移的。

- absolute:
将position设置为absolute后，该元素将脱离文档流，其位置是通过最近的非static定位的祖先元素来确定的。绝对定位可以设置margin，且不会和其他边距合并。常见形式是relative用作父元素，absolute作为子元素。

- fixed:
将position设置为fixed后，该元素将脱离文档流，相对于屏幕视口（viewport）来指定位置，且当屏幕滚动的时候，位置也不会改变，固定在相同的位置。

以下为几个我在《CSS世界中》看到的特别需要注意的是，

> - 当position设置为relative时，如果父元素的height为auto，则此时当前元素height设置为百分数将被解析为0。
> - 当position设置为relative时，由于默认的文档流是自上而下、从左往右，因此top/bottom同时使用时，bottom无效，left/right同时使用时，right无效。
> - 只有当position设置为absolute时，height属性设置为100%才有意义，否则height以百分比设置是无效的。
> - 当position设置为absolute时，float属性无效。此时元素具有流动性。此时如果同时设置left与right，top与bottom，或者四个一起做，其宽度高度就会去自动适应其父元素。
> - 当position设置为absolute或fixed，其display的计算值就是block或者table

一些应用

- 居中
利用前面提到的absolute元素自带流动性，外加margin:auto的自动居中的特性，可以写如下的CSS代码，该元素自动上下左右居中。

```js
.element {
    width: 300px;
    height: 300px;
    position: absolute;
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
    margin: auto;
}
```

- 浮动图标/窗口
利用fixed值可以写那种网页上常见的广告为，比如说分布于两列的广告，我个人实习的时候，写过信息提示的标签。

```js
.element {
    position: fixed;
    z-index: 2;
    left: ...;
    ...
}
```

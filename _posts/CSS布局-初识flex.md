---
title: CSS布局-初识flex
date: 2019-03-18 16:43:55
tags:
---

我的第一段实习的第一个任务是修改网页的适配性，使其能向下兼容到IE9，其中一个重要任务就是将flex布局的页面改成传统的流式布局的页面。那么flex布局是怎样的，这篇博客就来进行详细说明。
<!--more-->

## 模型介绍

## 容器的六种属性

1. flex-direction
控制弹性盒子内部元素的排列方向，计算值包括如下部分：

    1. row: 沿主轴从左向右

    2. row-reverse: 沿主轴从右向左

    3. column: 交叉轴从上到下

    4. column-reverse: 交叉轴从下到上

2. flex-wrap
默认情况下，项目都排在一条线，这个属性定义如果一条轴线排不下，如何换行：
    1. nowrap: 不换行
    2. wrap: 换行，第一行在上方
    3. wrap-reverse: 换行，第一行在下方

3. flex-flow
前两者的合一起的简写模式，默认值为row nowrap

4. justify-content
在主轴上的对齐方式，值如下，
    1. flex-start: 左对其
    2. flex-end: 右对齐
    3. center: 居中
    4. space-between: 两端对齐，项目之间间隔相等
    5. space-around: 每个项目两侧的间隔相等，边框有间隔
5. align-items

    1. flex-start: 交叉轴的起点对齐
    2. flex-end: 交叉轴终点对其
    3. center: 交叉轴中点对齐
    5. baseline: 项目第一行文字的基线对齐
    6. stretch: 如果未设置高度或设为auto，将占满整个容器的高度。

6. align-content
    1. flex-start：与交叉轴的起点对齐。
    2. flex-end：与交叉轴的终点对齐。
    3. center：与交叉轴的中点对齐。
    4. space-between：与交叉轴两端对齐，轴线之间的间隔平均分布。
    5. space-around：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。
    6. stretch（默认值）：轴线占满整个交叉轴。

## 项目的六种属性

1. order
排列顺序，数值越小，排列越靠前
2. flex-grow
属性定义项目的放大比例，默认为0，如果存在剩余空间，也不放大
3. flex-shrink
属性定义项目的缩小比例，默认为1，如果空间不足，项目将缩小
4. flex-basis
分配多余空间之前，项目占据的主轴空间。
5. flex
前四者的简写
6. align-self
属性允许单个项目有与其他项目不一样的对齐方式，可覆盖align-items属性。默认值为auto，表示继承父元素的align-items属性，指定后可使该元素对齐方式特殊化
值当不是auto时，与align-item一致

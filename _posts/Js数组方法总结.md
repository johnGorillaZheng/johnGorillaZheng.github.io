---
title: Js数组方法总结
date: 2019-04-14 16:00:06
tags:
---

本篇博客将总结js中常见的数组方法
<!--more-->

## 1. 基础函数

1. push
数组尾部添加元素，改变原数组，返回该数组的长度，可视为入栈
2. unshift
在数组头部添加元素，改变原数组，返回该数组的长度，可视为入队
3. pop
在数组尾部弹出元素，改变原数组，返回该弹出元素，可视为出栈
4. shift
在数组头部弹出元素，改变原数组，返回该弹出元素，可视为出队
5. slice(start, end)
返回索引为start～end-1的子数组，不改变原数组
6. splice(start, length, ele1, ele2, ele3 …)
返回从索引start起往后length位的数组，同时删除原数组的如上位置的元素，加入ele1往后传入的参数，过程改变原数组

## 2. ES6的函数

1. forEach(function callback(currentValue, index, array))
该函数返回undefined，会对数组的每个元素进行callback的操作，对原数组做改变
2. map(function callback(currentValue, index, array))
该函数将返回一个新生成的数组，会对数组的每个元素进行callback的操作，将处理的结果加入新生成的数组，不对原数组做改变
3. every(function callback(currentValue, index, array))
该函数将返回一个布尔值，会对数组的每个元素进行callback函数的判断，如果对于任意元素callback的处理结果都为true，则函数返回true
4. some(function callback(currentValue, index, array))
该函数将返回一个布尔值，会对数组的每个元素进行callback函数的判断，如果存在元素callback的处理结果都为true，则函数返回true
5. find(function callback(currentValue, index, array))
该函数将返回一个数组中的元素，会对数组的每个元素进行callback函数的判断，返回第一个判断为true的元素
6. filter(function callback(currentValue, index, array))
该函数将返回一个新生成的数组，会对数组的每个元素进行callback函数的判断，返回符合条件的全体元素的数组
7. sort(function compare(a, b))
该函数将对原数组进行排序处理，对与如何排序，做如下讨论，

- 如果compare() < 0, a应当出现在b前面
- 如果compare() > 0, b应当出现在a前面
- 如果compare() = 0, b与a出现在相同位置

    这里不仅可以针对纯数字字符串的函数进行判断，我们同样可以对对象进行排序，对比对象中的比较元素（比如ID），设置返回值正负，从而实现排序的目的。

8. reduce(function callback(accumulator, currentValue, currentIndex, array)) 将数组中的所有元素进行callback的处理，同时会对之前的结果进行累计的处理，也就是说有一种这样的即视感$f(x_i)=\sum_{i−1}^{i=0}f(x)+x_i$,但具体是加法还是其他操作就取决于callback了。

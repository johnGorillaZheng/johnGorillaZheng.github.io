---
title: JS语法糖总结
date: 2020-04-15 22:41:40
tags:
---

最近的实习中大量运用到一些JS的骚操作，现在把碰到的一些用法进行一下总结。

<!--more-->

我定义如下图的对象，方便下面的讲解

```js
const graph = {
  name: 'sample',
  nodes: {
    Beijing: {
      label: 'BJ',
      value: 1,
    },
    Tianjin: {
      label: 'TJ',
      value: 2,
    }
  },
  links: {
    Jingjin: {
      from: 'Beijing',
      to: 'Tianjin',
      weight: 10
    }
  },
}
```

## 参数解构

通过使用`{}`,我们可以更简洁地进行对象属性的引用

```js
// 这是过去的写法
const nodes = graph.nodes;
// 这是ES6以后的写法，可以进行多次引用
const { nodes, links } = graph;
// 也可以进行层级引用, 但只能取出每个块内最内的元素，下面只能取出北京
const { nodes: { Beijing } } = graph;
// 可以进行另赋值
const { nodes: cities } = graph;
```

## 拓展运算符与剩余参数`...`

这里摘抄一下MDN的说法,

>展开语法(Spread syntax), 可以在函数调用/数组构造时, 将数组表达式或者string在语法层面展开；还可以在构造字面量对象时, 将对象表达式按key-value的方式展开。(译者注: 字面量一般指 [1, 2, 3] 或者 {name: "mdn"} 这种简洁的构造方式)

### 函数参数

### 数组拷贝

基础用法

```js
const arr1 = [1,2,3];
// 以下就会生成 [1,2,3,1,2,3]
const arr2 = [...arr1, ...arr1];
```

通过展开运算符，我们可以更简单地构建在数组任意位置添加/删除元素地函数.当然，下面地例子会返回一个新的数组

```js
const add = (arr, index, val) => [
  ...arr.slice(0, index),
  val,
  ...arr.slice(index + 1,)
];

const del = (arr, index) => [
  ...arr.slice(0, index),
  ...arr.slice(index + 1,)
];
```

### 初始化对象

前面说到，展开运算符会将对象表达式按key-value的方式展开，这点在声明一个“继承”自原有对象的新对象特别好用，比如

```js
const newGraph = {
  ...graph,
  label: 'new',
}

```

此时声明的`newGraph`就会获得`graph`的所有属性且值相同，并且获得新属性`label`，但注意，这种拷贝是浅拷贝，（不拷贝`__prototype`）

## 可计算的属性名

如果需要动态地进行对象读写，比如同时写入新的键值对

```js
const city = 'Changsha';
const newGraph = {
  ...graph,
  [city]: {
    label: 'CS',
    value: 3,
  }
}
```

如此可以动态地改变对象内部地键值，即采用`[key]:value`这种形式去写。

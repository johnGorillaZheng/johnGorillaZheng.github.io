---
title: JS数据结构-队列与栈
date: 2018-07-02 09:54:42
tags:
---

我认为在学习一门新语言的过程中，用其实现常用的数据结构是一种快速深入这门语言的方法，同时还能复习一下数据结构与算法。这篇博客将讲解队列与栈的实现。

<!--more-->

# 基础代码实现

在实现的过程中我们首先需要选定承载数据结构的容器，然后再用这些数据结构所包含的规则对其方法进行定义。在这里，我们选用js的数组作为我们承载容器。

这里为了方便展示以及进行穿插讲解，我采用了构造函数法来定义类，并且将相应的方法进行逐步展示。

## 定义

### 队列的定义

```js
function Queue() {
    /*定义容器*/
    this.dataStore = [];
    /*定义入队方法*/
    this.enqueue = enqueue;
    /*定义出队方法*/
    this.dequeue = dequeue;
    /*定义队首*/
    this.front = front;
    /*定义队尾*/
    this.back = back;
    /*判断是否为空*/
    this.is_empty = is_empty;
    /*队列元素数量*/
    this.count = 0;
}
```

### 栈的定义

```js
function Stack() {
    /*定义容器*/
    this.dataStore = [];
    /*定义头指针*/
    this.top = 0;
    /*定义进栈方法*/
    this.push = push;
    /*定义退栈方法*/
    this.pop = pop;
    /*定义获得长度方法*/
    this.getLength = length;
    /*清空栈*/
    this.clear = clear;
}
```

## 具体方法的实现

### 队列的进出

队列是一种先进先出的线性结构。因此，在进行入队的操作时，加进的元素应从容器的尾部加入；在进行退队的操作时，退出的元素应从容器的头部退出。

```js
/*入队*/
function enqueue(element) {
    this.dataStore[this.count] = element;
    this.count ++;
}

/*退队*/
function dequeue() {
    for (var i = 0; i < this.count - 1; i ++) {
        dataStore[i] =dataStore[i++];
    }
    this.dataStore.splice(this.count - 1, 1);
    this.count --;
    return dataStore[0];
}
```

### 栈的进退

与队列相反，栈是一种先进后出的线性结构。所以在进行进栈的操作时，加进的元素应从容器的头部加入；在进行退栈的时候，也应从容器的头部退出

```js
/*入栈*/
function push(element) {
    this.dataStore[this.top] = element;
    this.top ++;
}

/*退栈*/
function pop() {
    var result = this.dataStore[this.top - 1];
    this.top --;
    return result;
}
```

---
title: JS数据结构-单向链表
date: 2018-07-29 09:58:50
tags:
---

这一篇将复习单向链表这一简单数据结构，并且用JS来实现。以后将逐步实现双向链表以及环链表。
基础代码实现
<!--more-->

## 定义

首先定义单向链表的节点，每个节点包含自身的值以及指向下一个节点的指针。

```js
function Node(element) {
    this.element = element;
    this.next = null;
}
```

对链表类进行定义，一个链表应包括一个头节点，以及方法，包括插入新节点，删除或找到某一个节点。

```js
function LList() {
    this.head = new Node("head");
    this.find = find;
    this.insert = insert;
    this.remove = remove;
    this.findPrevious = findPrevious;
}
```

## 具体的方法实现

### 找到某一节点

```js
function find(element) {
    var current = this.head;
    while(current.element != element){
        current = current.next;
    }
    return current;
}
```

### 插入新节点

```js
function insert(newElement, currentItem) {
    var current = this.find(currentItem);
    var newNode = new Node(newElement);
    newNode.next = current.next;
    current.next = newNode;
}
```

### 得到某一节点的前一节点

```js
function findPrevious(element){
    var current = this.head;
    while((current.next.element != element) && 
        (current.next != null)) {
        current = current.next;
    }
    return current;
}
```

### 移除某一节点

```js
function remove(element) {
    var previous = this.findPrevious(element);
    if(previous.next != null) {
        previous.next = previous.next.next;
    }
}
```

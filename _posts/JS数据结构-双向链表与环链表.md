---
title: JS数据结构-双向链表与环链表
date: 2018-07-31 10:01:50
tags:
---

这一篇将接着上一篇来实现双向链表，并且借由所实现的链表实现约瑟夫环问题。
基础代码实现
<!--more-->

## 节点类的实现

与单向链表不同，双向链表与环链表的节点有两个指针。按照两种链表的不同，节点两个指针指向不同。

function Node(element) {
    this.element = element;
    this.next = null;
    this.previous = null;
}

## 两种链表的实现

### 双向链表

```js
function DLList() {
    this.head = new Node("head");
    this.find = find;
    this.insert = insert;
    this.remove = remove;
    this.findLast = findLast;
}
```

### 环链表

```js
function LLList() {
    this.head = new Node("head");
    this.head.next = this.head;
    this.find = find;
    this.insert = insert;
    this.findPrevious = findPrevious;
    this.remove = remove;
    this.length = 0;
    this.currentNode = this.head;
    this.advance = advance;
}
```

## 双向链表的方法实现

### 找到某一节点

```js
function find(element) {
    var current = this.head;
    while (current.element != element){
        current = current.next;
    }
    return current;
}
```

### 插入节点

```js
function insert(newItem, currentItem) {
    var current = this.find(currentItem);
    var newNode = new Node(newItem);
    newNode.next = null;
    newNode.previous = current;
    current.next = newNode;
}
```

### 移除节点

```js
function remove(element) {
    var current = this.find(element);
    current.previous.next = current.next;
    current.next.previous = current.previous;
    current.previous = null;
    current.next = null;
}
```

### 找到链表中最后一个元素

```js
function findLast() {
    var current = this.head;
    while (current.next != null) {
        current = current.next;
    }
    return current;
}
```

## 环链表的方法实现

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

### 插入某一节点

```js
function insert(newElement, currentItem) {
    var current = this.find(currentItem);
    var newNode = new Node(newElement);
    newNode.next = current.next;
    current.next = newNode;
    this.length ++;
}
```

### 找到某一节点的前一节点

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

### 移除一个节点

```js
function remove(element) {
    var previous = this.findPrevious(element);
    if(previous.next != null) {
        previous.next = previous.next.next;
        this.length --;
    }
}
```

### 向前移动n个节点

```js
function advance(n) {
    while (n > 0) {
        if (this.currentNode.next.element == "head") {
            this.currentNode = this.currentNode.next.next;
        } else {
            this.currentNode = this.currentNode.next;
        }        
        n--;
    }
}
```

### 向后移动n个节点

```js
function back(n) {
    while (n > 0) {
        if (this.currentNode.next.element == "head") {
            this.currentNode = this.currentNode.previous.previous;
        } else {
            this.currentNode = this.currentNode.previous;
        }
    }
}
```

### 约瑟夫环的实现

```js
function JosefLoop(number) {
    var count = 3;
    var josefLoop = new LList();
    var current = josefLoop.head;

    //插入链表

    for (var i = 1; i <= number; i++) {
        if (i == 1) {
            josefLoop.insert(1, "head");
        } else {
            josefLoop.insert(i, i-1);
        }
    }
    while (josefLoop.length > 2){
        josefLoop.advance(count);
        josefLoop.remove(josefLoop.currentNode.element);
        josefLoop.display();
    }
}
```

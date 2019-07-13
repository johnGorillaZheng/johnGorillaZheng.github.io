---
title: JS数据结构-二叉树
date: 2018-09-07 10:06:50
tags:
---

从本篇起，我会尽我所能阐释那些复杂的数据结构，包括树与图，同时，从本篇起，会穿插地说一说其他的算法，例如排序，搜索，贪心等等，取决于我的思考程度以及leetcode做题程度（做的比较慢，总是想不出或总是debug失败。。。）。这一篇将会介绍树这一数据结构，本篇是基础篇，随后会有一篇树相关的算法。由于树中大量的运用到递归，总想不明白，目前leetcode只写出了很少的相关题。那么闲话少叙，现在开始。这里的树，仅限于二叉树。
<!--more-->

# 基础代码实现

## 基础树的定义

首先我们定义二叉树的节点。跟其他数据结构的节点相比，二叉树的节点应包括他本身的值，以及指向该节点左子树以及右子树的指针。

```js
function Node(data, left, right) {
    this.data = data;
    this.left = left;
    this.right = right;
}
```

然后，我们开始定义二叉树本身的内容，首先，指定根节点，然后定义相关方法。这里将整个问题进行简单化，在节点插入过程中自动通过数值的大小，自动将其变为二叉搜索树。

```js
function BST() {
    this.root = null;
    this.length = 0;
    this.insert = insert;
    //中序遍历
    this.inOrder = inOrder;
    //先序遍历
    this.preOrder = preOrder;
    //后序遍历
    this.postOrder = postOrder;
}
```

## 具体方法的实现

根据二叉树的定义“二叉树是n(n>=0)个有限结点构成的集合。n=0称为空二叉树；n>0的二叉树由一个根结点和两互不相交的，分别称为左子树和右子树的二叉树构成”可知，二叉树的遍历可以通过递归得到：只要所遍历的节点不为空，即可递归地查看它地左右两个子树，从而遍历整个二叉树。而对于已有的前序、中续以及后续三种遍历方法来说，所改变的是遍历左子树，查看节点自身以及遍历右子树，这三个子模块的顺序。

```js
//中序遍历
function inOrder(node) {
    if (node != null) {
        inOrder(node.left);
        print(node.show() + " ");
        inOrder(node.right);
    }
}

//前序遍历
function preOrder(node) {
    if (node != null) {
        print(node.show() + " ");
        preOrder(node.left);
        preOrder(node.right);
    }
}

//后序遍历
function postOrder(node) {
    if (node != null) {
        postOrder(node.left);
        postOrder(node.right);
        print(node.show() + " ");
    }
}
```

以下就是构造一颗搜索二叉树的插入节点的方法，大体思路就是不断的遍历节点，小了往左走，大了往右走，直到走到尽头，插进去，完事。

```js
function insert(data) {
    let newNode = new Node(data, null, null);
    //如果为空树，则将插入数据设置根结点
    if (this.root == null) {
        this.root = newNode;
        this.length ++;
    } else {
        let current = this.root;
        let parent;
        while (true) {
            parent = current;
            //当新节点的值小于根节点，往左插入
            if (data < current.data) {
                current = current.left;
                if (current == null) {
                    parent.left = newNode;
                    this.length ++;
                    break;
                }
            }
            //当新节点的值大于根节点，往右插入
            else {
                current = current.right;
                if (current == null) {
                    parent.right = newNode;
                    this.length ++;
                    break;
                }
            }
        }
    }
}
```

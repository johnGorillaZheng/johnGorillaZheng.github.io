---
title: JS数据结构-图
date: 2018-09-14 19:40:17
tags:
---

这一篇介绍图这一数据结构，之后也会放上leetcode的题。
<!--more-->

# 基础代码实现

## 基础类的定义

### 顶点类的定义

```js
function Vertex(label) {
    this.label = label;
}
```

### 图类的定义

```js
function Graph(v) {
    //顶点个数
    this.number_vertice = v;
    //边的条数
    this.number_edge = 0;
    //
    this.edgeTo = [];
    //用于存储图的邻接矩阵
    this.adj = [];
    //初始化邻接矩阵
    for (var i = 0; i < this.number_vertice; i++) {
        this.adj[i] = [];
        this.adj[i].push("");
    }
    //用于在搜索算法中判定是否已经被读过
    this.marked = [];
    for (var i = 0; i < this.number_vertice; i++) {
        this.marked[i] = false;
    }
    //构建边
    this.addEdge = addEdge;
    //将图以邻接矩阵的形式进行输出
    this.showGraph = showGraph;
    //深度优先搜索
    this.depthFirstSearch = depthFirstSearch;
    //广度优先搜索
    this.breadthFirstSearch = breadthFirstSearch;
}
```

## 方法的实现

### 增加边

```JS
function addEdge(v, w) {
    this.adj[v].push(w);
    this.adj[w].push(v);
    this.number_edge++;
}
```

### 将图以邻接矩阵的形式进行输出

```JS
function showGraph() {
    var str = "";
    for (var i = 0; i < this.number_vertice; i++) {
        str = i + "->";
        for (var j = 0; j < this.number_vertice; j++) {
            if (this.adj[i][j] != undefined) {
                str += this.adj[i][j] + " ";
            }
        }
        debug(str);
    }
}
```

### 深度优先搜索

```JS
function depthFirstSearch(v) {
    this.marked[v] = true;
    if (this.adj[v] != undefined) {
        debug("Vertex Visted:" + v);
        for (var i = 0; i < this.adj[v].length; i++) {
            if (!this.marked[this.adj[v][i]]) {
                this.depthFirstSearch(this.adj[v][i]);
            }
        }
    }
}
```

### 广度优先搜索

```JS
function breadthFirstSearch(vertex) {
    var queue = [];
    this.marked[vertex] = true;
    //将参数推到队列末尾
    queue.push(vertex);
    while (queue.length > 0) {
        //将队列首元素移除
        var top = queue.shift();
        if (this.adj[top] != undefined) {
            debug("Vertex Visited:" + top);
            for (var i = 0; i < this.adj[top].length; i++) {
                if (!this.marked[this.adj[top][i]]) {
                    this.marked[this.adj[top][i]] = true;
                    queue.push(this.adj[top][i]);
                }
            }
        }
    }
}
```

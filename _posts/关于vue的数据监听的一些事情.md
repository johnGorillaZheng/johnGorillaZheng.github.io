---
title: 关于vue的数据监听的一些事情
date: 2019-02-27 17:01:05
tags:
---

这是一篇回忆贴，主要是我以前在写vue时在写数据监听曾经踩过的坑，主要针对对象与数组的监听。本篇将设定一个生产生活中常用的场景，然后解决其中发生的问题。这是自己原创的一个小实验。
<!--more-->

由于实际json在实际的开发过程中承担着前后端沟通的数据沟通作用，实际生产中不可避免地会接触大量json以及数组的操作，那么对这些数据的监听就十分重要了。假设我们在一个模拟场景中需要对json进行处理，现在目前代码如下，基本上模拟出了以前实习以及之后写VUE代码踩过的坑。

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8"> 
    <title>我的vue自学代码集</title>
</head>
<body>
    <div id="lesson-doubleBind">
        <h4>{{ message}}</h4>
        <h2>json中字符串</h2>
        <h4>{{ message.str }}</h4>
        <input type="text" v-model="message.str">
        <h2>json中数组</h2>
        <h4>{{ message.arr }}</h4>
        <input type="text" v-model="message.arr[0]">
        <input type="text" v-model="message.arr[1]">
        <input type="text" v-model="message.arr[2]">
        <h2>json中二维数组</h2>
        <h4>{{ message.multiArray }}</h4>
        <input type="text" v-model="message.multiArray[0][0]">
        <input type="text" v-model="message.multiArray[0][1]">
        <h2>json中对象</h2>
        <h4>{{ message.obj }}</h4>
        <input type="text" v-model="message.obj.a">
        <input type="text" v-model="message.obj.b">
        <h2>一般数字</h2>
        <h4>{{ num }}</h4>
        <input type="text" v-model="num">
        <h2>一般二维数组</h2>
        <h4>{{ multiArray }}</h4>
        <input type="text" v-model="multiArray[0][0]">
        <button @click="submit1">后台改变数组变量</button>
        <button @click="submit2">后台改变一般变量</button>
        <button @click="submit3">后台改变对象变量</button>
    </div>

    <script src="vue.min.js"></script>
    <script>
        new Vue({
            el:'#lesson-doubleBind',
            data: {
                message:{
                    str: 'hello',
                    arr : [1,2,3],
                    multiArray: [[1,2],[3,4],[]],
                    obj : {
                        a : 1,
                        b : 2
                    }
                },
                num : 0,
                multiArray: [
                    [1,2],
                    [3,4]
                ]
            },
            watch: {
                message() {
                    console.log(this.message)
                },
                num() {
                    console.log(this.num)
                },
                multiArray() {
                    console.log(this.multiArray)
                }

            },
            methods: {
                submit1 : function() {
                    this.multiArray[0][0] -= 10;
                    console.log(this.multiArray);
                },
                submit2 : function() {
                    this.str = "has changed";
                    console.log(this.str);
                },
                submit3 : function() {
                    this.message.str = "obj str has changed";
                    this.message.arr[0][0] -= 10;
                    console.log(this.message);
                }
            }
        });
    </script>
    </body>
</html>
```

实际效果

![d](/image/vue_listening1.jpeg)

## 开始跳坑1 - 前台操纵数据

对于简单数据类型，直接在vue的watch元素中，加入以变量命名的函数，即可实现数据监听，前后台一致。不多说了。

```js
data : {
    message : Number
},
...
watch: {
    message function() {
        // your operation
    }
}
```

1. 对象的监听
    我们会发现，当在页面的输入框改写数值后，并不会触发监听函数。
    解决方法:将原来的message方法进行如下改写

    ```js
    message: {
        handler () {
            console.log(this.message);
        },
        deep : true
    },
    ```

2. 数组的监听
    这里就要进行分类讨论了，
    1. 数组是包含于一个对象的属性，此时，只需要按照前面进行对象监听即可
    2. 数组不属于任何对象，此时，在前台作用于数组的操作会被监听到。

## 继续跳坑2 - 后台操纵数据

1. 对象的监听
    需要按照上文方法设定监听变量。

2. 数组的监听
    又要分类讨论了，，
    1. 当操作包含数组的那些函数,会被自动监听到，例如：
    push(),shift(),pop(),slice(),splice()等
    2. 当对数组某一确定的值修改的时候，既不会被监听到，也不会作用于前台页面。当对数组某一具体值进行改变时，需要采用如下方法：

    ```js
        Vue.set(this.message.arr, 0, 'changed');           //对于一维数组
        Vue.set(this.message.multiArray[0], 0, 'changed'); //对于二维数组
    ```

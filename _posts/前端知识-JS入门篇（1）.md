---
title: 前端知识-JS入门篇（1）
date: 2018-05-19 21:16:27
tags:
---

这个系列将讲解一些基础js知识。
<!--more-->

# 最基础

## 一般的变量定义

```js
var a = ...;
//javascript不用强调int，float，double等类型，直接一个var解决
let b = ...;
//ES5开始可以用let来进行变量的定义，let可以让变量只作用于它所属的block及其子block，防止变量提示这种诡异的事情，提升代码维护性
```

## 数组的定义

```js
var arr = Array(length);    //初始化一个长度为length的数组,length也可以不填
var arr = Array(para_a,para_b,...); //初始化所有元素
var arr = [para_a,para_b,...]; //也可以直接不声明Array，数组的每个元素可以是不同的数据类型
```

## 关联数组

```js
var lennon = Array();
lennon["name"] = "John";
lennon["year"] = 1940;
lennon["living"] = false;
```

注意到，这个跟php中的关联数组有异曲同工之妙。

运算符，条件判断，循环跟其他语言基本一样，不赘述

## 面向对象

### 对象的声明

个人感觉个人javascript中对象的声明相比较java这种打着面向对象大旗的语言来讲相当随意，闲话不多说，开始解说。

既然说到面向对象，那么首先从类的定义开始讲起，javascript的类定义总共有3种，以下分别介绍3种定义方法以及相对应的对象声明方法

1. 构造函数法

    ```js
    function Tank() {
        this.name = "T-34";
        this.commander = "Zhukov";
    }
    //定义类函数
    Tank.prototype.fire = function() {
        console.log("biubiubiu");
    }
    //总感觉这种不把类函数写在类里的写法是异端
    ```

    生成实例时

    ```js
    var tank = new Tank();
    console.log(tank.name);   //输出结果T-34
    ```

2. Object.create()方法

    说明：在这种定义类的方法中，“类”被看作是一个对象，而不是函数。

    ```js
    var Tank = {
        name = "T-34";
        commander = "Zhukov";
        fire : function() {
            console.log("biubiubiu");
        }
    }
    //这个封装其实看的就舒服多了
    ```

    生成实例时，直接用Object.create()生成实例，不用new

    ```js
    var tank = Object.create(Tank);
    alert(tank.name);
    tank.fire();
    ```

    注意，这种写法在老式浏览器中不一定能用，如欲使用，需调用一下代码

    ```js
    if (!Object.create) {
        Object.create = function (o) {
            function F() {}
            F.prototype = o;
            return new F();
        };
    }
    ```

3. 极简主义法

    这种函数的写法，定义一个构造函数createNew()，然后把所有的属性，类函数放进去，一个类声明只有一个函数，一个函数将所有属性与函数说清楚，故曰极简主义。

    ```js
    var Tank = {
        createNew : function() {
            var tank = {};
            tank.name = "T-34";
            tank.fire = function() {
                console.log("biubiubiu");
            };
            return tank;
        }
    }
    ```

    生成实例时，直接调用createNew()方法

    ```js
    var tank = Tank.createNew();
    tank.fire();
    ```

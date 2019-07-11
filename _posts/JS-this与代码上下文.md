---
title: 'JS-this与代码上下文 '
date: 2019-04-11 16:31:38
tags:
---

这一篇博客将会探讨js中this对象在不同环境中的表现
<!--more-->

## 1. 指向本体还是全局?

假设我们有如下的函数，（很明显是个构造函数）

```js
function Person(name, age, id) {
    this.name = name;
    this.age = age;
    this.id = id;
}
```

如果我们直接通过函数调用的方法，那么此时应当分类讨论，如果在非严格模式下，这里的this相当于window对象，而如果是严格模式，则是undefined。

如果我们是通过new一个对象的方法，那么在新生成的对象里，this毫无疑问就应当指向当前对象了。值得注意的是，如果构造函数有返回值的话，当返回值为简单类型时，会忽略返回值，如果是对象，那么new出来的对象将会是这个返回来的对象，原因会在第三节讨论。

## 2. 在普通函数以及箭头函数中this的用法

对于普通函数来说，如果在对象中进行函数定义，作为一个对象的属性，这时this会自动绑定上级对象，但在函数中再进行函数定义的话，此时这个函数又将变成之前构造函数那种情况，this指向window或undefined。需要在函数外先将this进行赋值操作，再对新变量进行调用。

```js
var obj = {
    birth: 1990,
    getAge: function () {
        var b = this.birth; // 1990
        var fn = function () {
            return new Date().getFullYear() - this.birth; // this指向window或undefined
        };
        return fn();
    }
};
obj.getAge()  //NaN
```

对于箭头函数来说，函数体内的this对象，就是定义时所在的对象，对于如下例子来说，就是getAge的this，也就是指向obj

```js
var obj = {
    birth: 1990,
    getAge: function () {
        var b = this.birth; // 1990
        var fn = () => new Date().getFullYear() - this.birth; // this指向obj对象
        return fn();
    }
};
obj.getAge(); // 一个数字
```

再举个例子，此时作为setInterval函数的参数，箭头函数的定义范围其实是Person，如果new一个对象的话，this指向该对象，如果直接调用，则是指向window或undefined

```js
function Person() {
    this.age = 0;
    setInterval(()=>{console.log(this.age)}, 100);
}
```

再举一个绑定事件监听的案例，代码如下，此时作为addEventListener的参数，箭头函数的定义范围是init，此时this指向handler，如果不这么写，那么this的上下文将指定为使用范围，也就是document，此时就会报undefined的错误。

```js
var handler = {
    id: '123456',
    init: function() {
        document.addEventListener('click', event => this.doSomething(event))
    },
    doSomething: function(event) {
        console.log(event)
    },
}
```

同时，箭头函数不能使用call，apply方法。

## 3. this对象的改变 - call, apply, bind方法

之前在将js的继承的时候讲到过call与apply方法，它们会根据传入的第一个参数来指定函数上下文，然后在根据后面的参数函数在执行过程中的真正实参，不同的实call是一个一个的参数，而apply是一个数组，里面记录所有参数。

bind函数功能与前两者不同，它会生成一个新的函数，其内容与原函数保持一致，并绑定在传入的对象上。

现在可以看一看new关键字在生成对象时都做了什么。当构造函数调用时，会执行如下操作

- 创建一个全新的对象
- 对这个对象执行prototype连接
- 通过call方法改变this指向这个新对象，并进行对象的赋值
- 如果函数没有返回其他对象，则返回这个新对象

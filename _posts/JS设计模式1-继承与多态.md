---
title: JS设计模式1 - 继承与多态
date: 2019-02-27 17:03:53
tags:
---

在以前学习 java 的继承概念的时候，我了解到继承是设置子类使其继承父类，使得子类能够从父类继承方法和属性。而且在 java 以及 C++语言中，继承关系比较简单粗暴，一个 extend 或 inherit 关键字就把继承十分明显的表示出来。但在 JS 中，由于其特殊的机制，类之间的继承不是简单的复制，类的继承彼此间会存在关系，且不同的方法会表现出不同程度的相互影响。

<!--more-->

## 原型链继承

这里采用一下 MDN 相关教程的代码，定义 Person 这么一个类。其中类里面所有的方法都定义在构造器原型上

```js
function Person(first, last, age, gender, interests) {
    this.name = {
        first,
        last
    };
    this.age = age;
    this.gender = gender;
    this.interests = interests;
    this.greeting = function() {
        console.log("Hi, I'm" + this.first);
    };
}
```

在定义了人这个类之后，我们就可以着手实现相关子类了，比如 MDN 里的 Teacher，对于老师这个类来说，会多一个 subject 属性，同时也会改写原来的 greeting 方法。直接上代码

```js
function Teacher(first, last, age, gender, interests, subject) {
    Person.call(this, first, last, age, gender, interests);

    this.subject = subejct;
}
```

关于 call(),以及相关的 apply()和 bind() （照搬《深入理解 JavaScript》）

- Function.prototype.call(thisValue, arg1, arg2, …)
    第一个参数指定当前 bind 函数指向谁，如果是 this，则为当前类，如果为 null 则是全局对象（window 或 global）也可以指其他函数或对象，指当前函数。然后指定后面的参数参与函数运算。

- 在这里的作用，借用《Javascript 设计模式》这本书来说，就是“将子类中的变量在父类中执行了一遍”，子类就继承了父类的属性。的确，它在生成实例的时候，会调用父类的构造函数，以此将父类元素定在了子类里。

    从形式上看，和 java 中的 super()函数有些异曲同工之妙。

- Function.prototype.apply(thisValue, argArray)
    第一个参数同上，第二个参数是一个数组。注意：他只对函数或方法又用，对构造函数无效。

- Function.prototype.bind(thisValue, arg1, arg2, …)
    第一个参数同上，这个函数会生成并返回一个新函数，同时将从 arg1 开始的参数指定为原函数的固定参数，例如：

    ```js
    function add(x, y) {
        return x + y;
    }

    var plus = add.bind(this, 1); // 相当于原函数变成add(1, y)， x被指定为1
    plus(6); // 结果是7
    ```

此时有一个问题，Teacher()的 prototype 尚未指定，由于我们把方法绑定到了 prototype，所以方法并没有继承下来。需要做以下操作

```JS
Teacher.prototype = Object.create(Person.prototype); //创建一个和Person.prototype一样的新的属性值
Teacher.prototype.constructor = Teacher;
```

至于方法重写，完全可以根据实际需要去定义

## 于在 java 中碰到的公有变量，私有变量，特权方法的实现

```JS
function constructr(...) {
   var that = this;            //类内部的函数会需要调用指向类的指针
   var privateData = ...;      //虽然没有java那么直白，但私有数据是无法从外部访问的，只能对象内部进行修改
   this.publicData1 = ...;     //共有数据就可以直接obj.publicData1访问到
   this.privilegedFunction(...) {
       privateData = ...
       that.publicData = ...
   }
}
```

## 深入探讨：原型模式

在上面的继承的说明时，注意到子类在生成实例的时候会完整调用父类的构造函数。那么，当父类的构造函数逻辑复杂或者性能消耗巨大的时候，我们就不得不限制子类实例的生成。所以，我们在父类的编写时，可以把简单的属性赋值放入构造函数，复杂的函数放入父类的原型 prototype 中，通过 prototype 的原型链自动使子类拥有父类的函数，具体针对上文提到的 Person 类，就可以做如下改写：

```JS
function Person(first, last, age, gender, interests) {
    this.name = {
        first,
        last
    };
    this.age = age;
    this.gender = gender;
    this.interests = interests;
}

Person.prototype = {
    greeting : function() {
        console.log("Hi, I'm" + this.first);
    },
    anotherFunction : function() {},
    ...
}
```

而作为子类 Teacher,我们还需要前面提到的一步将 prototype 进行赋值的操作,就会自动共享 Person.prototype 的所有方法，然后根据实际需要再自行改写即可，

原型赋值

```JS
//推荐这种写法，因为减少了不必要的赋值操作，我们只想把函数通过原型进行继承
Teacher.prototype = Object.create(Person.prototype);
//或者
Teacher.prototype = new Person();
```

改写方法

```JS
Teacher.prototype.greeting = function() {
    console.log("Hi, I am a teacher");
};
Teacher.prototype.shake = function() {
    console.log("shake hands");
};
```

除此之外，当修改父类 prototype 的方法时，例如增加一个方法，子类会自动继承，但修改子类并不会影响父类，同时修改子类的函数并不会影响到父类的同名函数。这就设计到一个原型链的问题，对于 Teacher 的 prototype，它的具体成员可以这么看，

```JS
Teacher.prototype :{
    greeting : function () {
        console.log("Hi, I am a teacher");
    },
    shake : function() {
        console.log("shake hands");
    },
    __proto__ : {
        greeting : function() {
            console.log("Hi, I'm" + this.first);
        },
        anotherFunction : function() {},
        ...
    },
}
```

我们可以清晰地看到，proto这个对象存储着它父类所有的函数，其实它可以一直追溯到Object这个最原初的对象。而JS在调用一个对象的方法时，首先看它自己的prototype有无此方法，如果没有，就一级一级的父元素找，直到找到或走到Object宣布无方法。

最后，还有一种更节省资源的方法，特别是想进行某种多重继承的时候，直接对所有父类的原型进行浅复制然后生成实例。

```js
function prototypeCopy () {
    var F = function() {};
    var args = arguments;
    var len = args.length;

    for (var i = 0; i < len; i++) {
        for (var j in ars[i]) {
            F.prototype[j] = args[i][j]
        }
    }
    return Object.create(F);
}
```

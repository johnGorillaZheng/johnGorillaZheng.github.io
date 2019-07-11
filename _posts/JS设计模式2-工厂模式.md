---
title: JS设计模式2 - 工厂模式
date: 2019-03-02 16:50:16
tags:
---

这篇文章开始，会开始陆陆续续介绍我对设计模式的一些理解，以及用JS实现。主要参考书为张容铭的《Javascript设计模式》以及在线的《Graphic Design Patterns》。这篇文章会从简单工厂模式，工厂方法模式一直到抽象工厂模式，聊一聊官方的定义以及我自己的一些理解。

<!--more-->

## 简单工厂模式

根据定义，简单工厂模式需要专门定义一个类来负责创建其他类的实例，可以根据参数的不同返回不同的实例。不考虑JS没有JAVA的接口以及抽象类的情况下，有如下角色，

- Factory: 负责创建所有实例的内部逻辑

- Product: 负责描述所有实例所共有的公共接口

- ConcretProduct: 所有创建的对象都充当这个角色的某个具体类的实例

个人认为在JS语言中实现Product父类，再实现子类的做法有些累赘，毕竟工厂类并不会通过父类创建子类，而是直接作用于子类，父类并不会被使用，也就没有所谓的消解耦合的作用。

代码：

```js
function Factory(arg1, arg2, arg3 ...) {
    if (argsCondition1) {
        return new ConcretProduct1(arg1, arg2, arg3 ...);
    } else if (argsCondition2) {
        return new ConcretProduct2(arg1, arg2, arg3 ...);
    } else if (argsCondition3) {
        return new ConcretProduct3(arg1, arg2, arg3 ...);
    } ...
}

function ConcretProduct1(arg1, arg2, arg3 ...) {
    let attribute1 = null;
    let getSomething = function () {
        ...
    }
}

function ConcretProduct2(arg1, arg2, arg3 ...) {
    let attribute1 = null;
    let getSomething = function () {
        ...
    }
}
```

优点：

1. 客服端可以避免直接创建产品实例，从而实现代码分离，降低耦合。

2. 客户端不必知道产品的具体类名，而是可以通过一个工厂实例进行参数传递，通过参数将创建工作交给工厂实例。

缺点：

1. 工厂类集中了所有产品创建逻辑，一旦不能正常工作，整个系统都要受到影响。

2. 由于每个具体产品都需要定义类，所以系统的类个数势必增多，一定程度增加了系统的复杂度和理解难度。

    系统扩展困难，增加新产品必须修改逻辑，可能使工厂类出现很多if else的组合，不利于扩展维护。

## 工厂方法模式

上面说了简单工厂系统扩展困难的问题，在我看来本质上是一种耦合度高的问题，所以在工厂方法模式中，不再使用工厂类去负责所有产品的创建，而是将其交给专门的工厂负责子类去完成。等于说，工厂模式仅仅相当于创建具体工厂的接口，在具体工厂中再真正创建产品。相当于在简单工厂的基础上又多了一层，达到了降低耦合的目的。

```js
function Factory(type, content) {
    if (this instanceof Factory) {
        let s = new this[type](content);
        return s;
    } else {
        return new Factory(type, content);
    }
}

Factory.prototype = {
    ConcretFactory1: function(content) {
        this.content = content;
        (function(content){
            //operation
        })();
    },
    ConcretFactory2: function(content) {
        this.content = content;
        (function(content){
            //operation
        })();
    },
    ...
}
```

优点：

1. 相比于简单工厂模式，用户只需要关心所需产品对应的工厂，不用关心创建细节，无需知道具体产品类的类名。

2. 工厂与产品同时具有多态性，工厂可以自主确定创建何种产品对象，如何创建在具体工厂内部。

3. 添加新产品时，无需修改工厂类，只需要添加具体工厂及相应具体产品。相比简单工厂而言，拓展性变得很好。

缺点：

1. 添加新产品，需要编写新的具体工厂及具体产品 ，一定程度增加了系统的复杂度，系统开销可能增大。同时相应的实现难度也会增加。

印象比较深刻的例子，是《JS设计模式》里讲的设计按钮的例子，这个我自己在实际开发中亲身写过相关的代码，所以比较能够理解。实践过程中，我们不用将按钮的创建过程直接写在业务端，大幅提升业务端代码的可读性，使得维护性得到提升。
抽象工厂模式

无论是简单工厂还是工厂方法，两种设计模式都是针对某一具体产品进行操作，然而当产品体系复杂起来，比如考虑到增加多个产品层级与复杂结构，就算是工厂方法，扩展性和维护性也能想象到会变得异常庞杂。

代码

```js
//抽象工厂簇，存储所有抽象工厂，可以自动建造
function AbstractFactoryCluster(subType, superType) {
    if(typeof AbstractFactory[superType] == 'function') {
        //创建缓存类
        function F() {};
        //继承父类属性方法
        F.prototype = new AbstractFactoryCluster[superType]();
        //
        subType.constructor = subType;
        //子类原型继承父类
        subType.prototype  = new F();
    } else {
        throw new Error('未创建该抽象类');
    }
}
//
AbstractFactoryCluster.AbstractFactory  = function() {
    this.type = '';
}

AbstractFactoryCluster.AbstractFactory.prototype = function() {
    operation1 : function () {},
    operation2 : function () {}
    ...
}

}
```

优点：

1. 增加新的具体工厂和产品族很方便，无需修改已有系统

2. 实现了高内聚低耦合的设计目的。客户与具体类被隔离，更换具体工厂变得相对容易。

缺点：

1. 增加新的工厂和产品容易，直接添加即可。但增加新的产品簇，则操作复杂。

2. 结构复杂，使得有时候添加新产品会变得难以扩展。

以上均是官方说法，我由于目前所经历的实习以及做过的项目并没有庞大到一定地步，所以对此没有什么深度的理解与感悟。还是得积累经验，慢慢地去体会这些设计模式的存在意义。

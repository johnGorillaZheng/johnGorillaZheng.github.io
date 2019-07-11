---
title: 前端知识-JS入门篇（2）
date: 2018-05-19 21:25:49
tags:
---

在html中，一份html文档可以看作一个根结点为\的树，其所包含的诸如id，class以及包含的内容，甚至是下一层的标签，皆为其子树。
<!--more-->

# DOM基础

## 基本概念

```js
<p id="article">这是一段文档</p>
```

这段代码可以看作一棵树，表现形式如下

```html
      p
     / \
    /   \
  id  这是一段文档
 ／
／
article
```

其中，我们将整个的p标签所对于的东西称之为一个DOM元素，而这个DOM元素本身只是整个html树的一部分。像id或者p标签所包含的具体文章，我们称之为节点。当然节点和元素的概念是相对的。考虑如下结构的html代码，

```html
<div>
    <p id="article">
        hello world
    </p>
</div>
```

p标签对应的block是div对应block的一个子block。这时，我们可以说p是div的一个子节点。

## DOM节点的获取

1. getElementById()方法

    此方法将返回一个对象

    示例：

    ```js
    <ul id="example">
        ...
    </ul>
    <script>
        alert(typeof document.getElementById("example"));
    </script>
    ```

    此方法将展示出通过getElementById()方法获得的数据类型，结果应该是object对象数据类型,其中内涵id为example这个无序列表中的所有信息。

2. getElementByTagName()方法

    此示例为得到该页面中所有li标签，返回将是一个数组

    ```js
    document.getElementsByTagName(String : Tagname);
    ```

    因为此方法返回结果是数组，所以可以做类似的操作

    ```js
    var items = document.getElementsByTagName("li");
    var items_length = items.length;
    for(var i = 0; i < items_length; i++) {
        alert(typeof items[i]);
    }
    ```

    毕竟，我们会在一个页面中用多个tag，比如大量的div。所以个人感觉这么操作仅适用于那些比较特殊的tag，比如有序无序列表，毕竟拿到所有div效率会比较低下。

3. 获取与设置DOM对象内的值：getAttribute()与setAttribute()方法

    示例：

    ```js
    function showGetAttribute() {
        var idName = document.getElementById('show_chapter2').getAttribute('id');
        document.getElementById("show_inner").innerHTML = idName;
    }
    function showSetAttribute() {
        document.getElementsByTagName("h2")[0].setAttribute("class", "democlass");
    }
    ```

## 添加

既然能获取，就可以动态往DOM元素中进行添加，涉及到的函数如下

1. createElement方法

    ```js
    document.createElement(String : Tagname)
    ```

    注意，个人感觉这相当于一个创建DOM元素的语句，一般需要声明一个变量，把这个函数返回的值赋给这个变量，这样就可以在JS环境下自由操控这个元素。

2. createTextNode方法

    ```js
    document.createTextNode(String : Text)
    ```

    这个函数将创建一个文本节点，注意，这并不是一个DOM元素，而只是一个DOM元素的一个节点，不懂的话请看上面的树状说明图。跟前一个函数一样，一般也需要声明一个变量并进行赋值，除非放弃代码可读性。

3. appendChild方法

    ```js
    DOMElement.appendChild(Object : newNode)
    ```

    这个函数将在DOMElement的末尾添加一个节点。这通常是操作类函数，很少进行赋值，当然它的返回值就是被添加过节点的DOMElement。

4. insertBefore方法

    ```js
    node.insertBefore(newnode,existingnode)
    ```

    这个函数将在指定的node的范围内，对已有的子节点existingnode前插入一个新的子节点newnode。

## 一次小实践：通过javascript直接生成一个div

```js
function createElementInDiv() {
    //创建一个节点para，标签为p,注意，此节点为空
    var para = document.createElement("p");
    //创建一个节点para，标签为p,注意，此节点为空
    var emph = document.createElement("em");
    //得到id为testDiv的div
    var testDiv = document.getElementById("testDiv");
    //创建一个文本节点，内容为”This is“
    var text1 = document.createTextNode("This is ");
    //创建一个文本节点，内容为“my”
    var text2 = document.createTextNode("my ");
    //创建一个文本节点，内容为“content”
    var text3 = document.createTextNode("content");
    //将节点text1插入para节点
    para.appendChild(text1);
    //将节点text2插入emph节点
    emph.appendChild(text2);
    //将节点emph插入para节点
    para.appendChild(emph);
    //将节点text3插入para节点
    para.appendChild(text3);
    //将节点para插入测试div
    testDiv.appendChild(para);
}
```

它就会产生下面这么一段html代码

```js
<div id="testDiv">
    <p>
        Thi is
        <em>
            my
        </em>
        content
    </p>
</div>
```

在此附上自己建造（照搬）insertAfter方法

```js
function insertAfter(newElement, targetElement) {
    var parent = targetElement.parentNode;
    if(parent.lastChild == targetElement) {
        parent.appendChild(newElement);
    } else {
        parent.insertBefore(newElement, targetElement.nextSibling);
    }
}
```

最后提醒一下，由于前端开发涉及到跨浏览器的问题，因而需要判断浏览器支不支持这些DOM操作，其实主要是应对某些上古浏览器的适配问题。

记得在每个DOM操作函数的最开始加上如下代码

```js
if(!document.createElement) return false;
if(!document.createTextNode) return false;
if(!document.getElementById) return false;
if(!document.getElementById("imageGallery")) return false;
...
```

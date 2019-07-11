---
title: JS事件处理
date: 2019-03-25 16:35:43
tags:
---

这篇博客将介绍事件队列以及事件捕获之类的相关的知识，很多内容摘自《Javascript忍者秘籍》。

<!--more-->

## 事件的分类以及事件队列

浏览器在处理事件的时候会自动将事件添加进事件队列，然后按照先进先出的原则处理队列中的事件。需要注意的是，任务具有原子性，即执行时不可被中断，除非关机。

事件的循环包括两个队列，一个是宏任务队列，另一个是微任务队列。宏任务包括但不限于“创建主文档对象，解析HTML，执行全局Javascript代码，网络事件，定时器事件等待。”它是属于浏览器层面的任务。对于定时器来说，常见的有setTimeout和setInterval，一个是等一定的事件执行一次，另一个则是每间隔一定时间就会执行一次，需要注意的是当队列事件较多且setInterval要执行的事件已经在队列中的话，不会重复入队直到该事件被请出队列。

微任务则是用于更新应用程序的状态，通常在浏览器任务执行后执行。微任务必须“尽可能快地，异步地执行且不能产生全新的微任务”，包括DOM的变化以及Promise等。

执行顺序是宏任务-微任务-渲染UI，注意，在微任务队列清空前，不会渲染UI。宏任务会产生一系列的微任务，当一个宏任务执行完毕后会立刻处理微任务。

## 一个特别牛逼的应用：使用计时器来中断一个长时间运行的任务

这个案例来自于《Javascript忍者秘籍》清单13.4 - 13.5，借用此案例来讲解一下。

第一个是个bad case，需要长时间运行，该代码会生成一个20000行，每行6列，共计240000个节点。

该代码来自《Javascript忍者秘籍》清单13.4

```js
const tbody = document.querySelector("tbody");
for (let i = 0; i < 20000; i++) {
    const tr = document.createElement("tr");
    for (let j = 0; j < 6; j++) {
        const td = document.createElement("td");
        td.appendChild(document.createTextNode(i + "," + j));
        tr.appendChild(td);
    }
    tbody.appendChild(tr);
}
```

代码简介明了，就是会挂载很长时间，此时浏览器就会呈现出卡死状态，用户很有可能会不耐烦地关掉网页。下面提供一种中断的方式。

该代码来自《Javascript忍者秘籍》清单13.5

```js
const rowCount = 20000;
const divideInto = 4;
const chunkSize = rowCount / divideInto;
let iteration = 0;
const table = document.getElementByTagName("tbody")[0];
setTimeout(function generateRows() {
    const base = chunkSize * iteration;
    for (let i = 0; i < chunkSize; i++) {
        const tr = document.createElement("tr");
        for (let j = 0; j < 6; j++) {
            const td = document.createElement("td");
            td.appendChild(document.createTextNode((i + base) + "," + j));
            tr.appendChild(td);
        }
        table.appendChild(tr);
    }
    iteration ++;
    if (iteration < divideInto)
        setTimeout(generateRows, 0);
},0)
```

这就把13.4的庞大操作分割成了4个不连续的小操作，页面可以通过间隙进行渲染，这样页面不会看上去很卡。按书中的话讲，用户一般觉察不到这种变化，哈哈，果然和用户是需要斗智斗勇啊。
事件捕获与事件冒泡

```html
<html>
    <head></head>
    <body>
        <div id="outer">
            <div id="inner">
            </div>
        </div>
        <script>
            const outer = document.getElementById("outer");
            const inner = document.getElementById("inner");

            inner.addEventListener("click", ()=>{console.log("inner")});
            outer.addEventListener("click", ()=>{console.log("outer")});
            document.addEventListener("click", ()=>{console.log("document")});
        </script>
    </body>
</html>
```

考虑如上代码，当点击inner时，浏览器存在两种处理方式。

- 捕获：首先被顶部元素捕获，然后依次向下传递直到目标元素，控制行会依次输出document，outer，inner
- 冒泡：首先被目标元素捕获，然后向顶部元素传递，W3C标准倾向于这种inner，outer，document

《忍者秘籍》这本书告诉我们，对于addEventListener这个函数，实际上还存在第三个参数，是一个布尔值，默认false。当该值为true，进行捕获处理，当设置为false时，会进行冒泡处理。

这时，我们对代码稍加改动，如下

```js
inner.addEventListener("click", (event)=>{
    console.log(this);
    console.log(event.target);
});
outer.addEventListener("click", (event)=>{
    console.log(this);
    console.log(event.target);
});
```

这时如果点击inner时，首先会输出两个inner，当事件冒泡到outer时，对于this对象，此时输出outer，而event.target会输出inner。一个指向当前执行对象，一个指向切实的发送对象。

## 应用

当我们要对一个大容量的表格或列表进行事件的监听时，不用对每一个单项都设置事件监听，利用事件冒泡加event.target直接将处理绑定到表格或列表项，这样可以节约内存资源。

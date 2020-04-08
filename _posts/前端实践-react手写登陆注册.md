---
title: 前端实践-react手写登陆注册
date: 2019-07-20 21:40:56
tags:
---

最近在澳大利亚一个初创团队做前端，正在参与并见证一个网站从0到1的全过程。其中不少功能都是自己设计并编写，其中登陆注册的操作便是由我搞定的，特此进行一些总结，希望对未来开发有所帮助。*这里没有涉及到后端的知识，以后查阅此文后，务必与对接的后端工程师进行详细的接口讨论。本篇文章只提供大致的思路。

<!--more-->

## 大体思路

对于第一次注册与登陆的用户，其待开发的事情包括：

1. 根据具体需求创建表单，并通过axios实现表单内容上传至后端。

2. 通过表单自带的方法或者自己写的方法进行表单各项数据的检查。

3. 对返回的response进行判断，如果response为200成功，则根据具体需求进行当前用户数据的存储，比如存到cookie或localstorage，以及redux或vuex这样的状态管理器，并做页面跳转；如果失败，则将后端返回的错误信息进行展示，提示用户作出修改。

对于非第一次登陆的用户，其待开发的事情包括：

1. 检查当前cookie或localstorage中是否存有用户信息，以及是否过期。Cookie由于本身自带过期时间属性，且过期后浏览器会自动将其清空，故只需检查是否存在。localstorage则需要检查字段中所携带的时间信息，与当前时间进行比对，并根据过期与否做清空操作。

2. 如果不存在或过期，则要求用户重新登陆。

3. 如果存在且没过期，则取出相应的用户信息，状态设置为已登陆。

## 基础功能的实现

1. cookie的操作

    如果cookie被设置为可被js访问，那么js将访问到一个字符串，其中含有所有cookie的内容。由于cookie的添加基本上由后端response中header的set-cookie这个字段完成，故一般的操作为查询与删除。

    cookie的查找主要是对字符串进行操作，过程比较简单，思路略。。。

    ```js
    export const hasCookie = str =>
    document.cookie.split(';').some(token => token.indexOf(str) !== -1);
    ```

    ```js
    export const getCookie = str => {
    const cookies = document.cookie.split(';');
    for (const token of cookies) {
        if (token.indexOf(str) !== -1) {
        return token.split('=')[1];
        }
    }
    return false;
    };
    ```

    cookie的清除主要思路是重新设置其过期时间，使之早于当前时间，让浏览器自己删掉cookie。

    ```js
    const logout = () => {
        const current = new Date();
        current.setTime(current.getTime() - 1);

        const cookies = document.cookie.split(';');

        cookies.forEach(cookie => {
        const name = cookie.trim().split('=')[0];
        const value = cookie.trim().split('=')[1];
        const time = current.toGMTString();
        document.cookie = `${name}=${value};expires=${time};path=/`;
        });
        window.location.href = '/';
    };
    ```

2. localstorage的操作

    localstorage自己已经集成了添加，获取以及清除所有的功能，调用即可，至于取到的字符串，则根据实际情况进行操作。

    ```js
    localStorage.setItem('key', 'value');
    localStorage.getItem('key');
    localStorage.clear();
    ```

## 生命周期的操作

大体思路是在组件挂载阶段就进行登陆状态的判定，使导航栏根据判定结果决定渲染的东西。比如导航栏是渲染登陆注册的链接，还是用户选项的下拉菜单。

这里用functional component来实现，比较简洁。以下只是一个示例，在实际的操作中，判断是否登陆的函数可能会需要与redux相结合，同时，登陆的判断也可用于有登陆要求的页面渲染，可以与reacter dom router相结合。

```js
export default function Sample() {
    const [isLoggin, setIsLoggin] = useState(false);
    useEffect(() => {
        setIsLoggin(!!getCookies('username') || !!localStorage.getItem('username'));
    }, []));

    return (
        {isLoggin ? <ComponentLogin /> : <ComponentNotLogin />}
    );
}

```

## 表单的创建与发送

这个部分可以参考以后写的表单数据发送的内容。敬请期待。

关于cookie的相关信息，也敬请期待。

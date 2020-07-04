---
title: 前端实践-目前踩过的cookie的坑
date: 2019-08-06 23:33:23
tags:
---

## Cookie是怎么工作的

按照我目前做过的工作来说，cookie的工作流程大体上包括创建，保存与销毁。

实际工作中，cookie往往是会在HTTP Response的Header中对浏览器进行设置，在Header中，会加上一个 Set-cookie的字段，例如下面这个例子，

```bash
HTTP/1.0 OK
Set-cookie: id='1234'; domain='example.com'; ...
Content-type: text/html
...
```

这样就给目标用户贴上了一条cookie信息。cookie具体包括以下的属性，

- NAME=VALUE

- Domain
    可以访问此cookie的域名
- Max-age
    设置以秒计算的cookie存在时长，当max-age为正数时，

- Path
    可以访问此cookie的页面路径
- secure
    如果设置，那么该cookie只能在HttpS的情况下发出。

- HttpOnly
    意思是cookie只能被http方法所操作。如果设置，则浏览器端的JS将无法访问该Cookie。但当向服务器发送请求时，可以将cookie带上。

## Cookie的浏览器端JS基本操作 - 增删改查

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

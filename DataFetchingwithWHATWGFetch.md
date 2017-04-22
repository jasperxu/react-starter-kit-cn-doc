# 使用WHATWG Fetch获取数据
isomorphic `core/fetch` 模块可以在客户端和服务端使用相同的代码，如下：
```js
import fetch from '../core/fetch';

export const path = '/products';
export const action = async () => {
  const response = await fetch('/graphql?query={products{id,name}}');
  const data = await response.json();
  return <Layout><Products {...data} /></Layout>;
};
```
当该代码在客户机上执行时，Ajax请求将通过GitHub的[fetch](https://github.com/github/fetch)库（`whatwg-fetch`）发送，该库（本身在该场景下使用XHMLHttpRequest），除非用户浏览器本机支持`fetch`。

无论何时在服务器上执行相同的代码，它将在场景后面使用[node-fetch](https://github.com/bitinn/node-fetch)模块，该模块本身通过Node.js `http`模块发送HTTP请求。 它还将相对URL转换为绝对值（请参阅`./core/fetch/fetch.server.js`）。

`whatwg-fetch`和`node-fetch`模块都有几乎相同的API。 如果您是这个API的新用户，以下文章可能会给您一个很好的介绍：https://jakearchibald.com/2015/thats-so-fetch/
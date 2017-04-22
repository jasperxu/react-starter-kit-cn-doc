# Universal Router入门
这个路由器是围绕Express和Koa所使用的中间件方法构建的，所以如果你已经熟悉了这些框架，那么学习Universal Router应该很轻松。 以下代码示例假定您通过[Babel](http://babeljs.io/)使用ES2015的JavaScript风格。

您可以通过运行以下方式通过npm安装`Universal Router`库：
```shell
$ npm install universal-router --save
# 或
$ yarn add universal-router
```
该模块包含一个具有单个`router.resolve`方法的`Router`类，该方法负责遍历路由列表，直到找到与提供的URL路径字符串匹配的第一条路由，并且其操作方法返回除`null`或`undefined`之外的任何内容。 每个路由只是一个具有`path`, `action`, 和`children`（可选）属性的纯JavaScript对象。
```js
import Router from 'universal-router';

const routes = [
  { path: '/one', action: () => '<h1>Page One</h1>' },
  { path: '/two', action: () => '<h1>Page Two</h1>' },
  { path: '*', action: () => '<h1>Not Found</h1>' }
];

const router = new Router(routes);

router.resolve({ path: '/one' }).then(result => {
  document.body.innerHTML = result;
  // renders: <h1>Page One</h1>
});
```
如果您使用Node v5及更低版本的路由器，请按如下所示导入：
```js
import Router from 'universal-router/legacy';
```
如果您不想使用npm来管理客户端软件包，则`universal-router`npm软件包还提供托管在CDN上的单个文件分发：
```html
<script src="https://unpkg.com/universal-router/universal-router.js"></script>
<script src="https://unpkg.com/universal-router/universal-router.min.js"></script>
<script src="https://unpkg.com/universal-router/universal-router-generate-urls.js"></script>
<script src="https://unpkg.com/universal-router/universal-router-generate-urls.min.js"></script>
```
注意：您可能需要包含[Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)，[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)和[Object.assign](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) polyfills以兼容旧版浏览器。

## 使用React
```js
import React from 'react';
import ReactDOM from 'react-dom';
import Router from 'universal-router';

const routes = [
  { path: '/one', action: () => <h1>Page One</h1> },
  { path: '/two', action: () => <h1>Page Two</h1> },
  { path: '*', action: () => <h1>Not Found</h1> }
];

const router = new Router(routes);

router.resolve({ path: '/one' }).then(component => {
  ReactDOM.render(component, document.body);
  // renders: <h1>Page One</h1>
});
```

## 学习更多
[Universal Router API](https://github.com/kriasoft/universal-router/blob/master/docs/api.md)









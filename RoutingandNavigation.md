# 如何实现路由和导航[](https://github.com/pillarjs/path-to-regexp)
让我们来看看一个100行代码以内的自定义路由解决方案大概是什么样子的。

首先，您需要实现**list of application routes**，其中每个路由都是一个对象，其具有`path`属性（参数化URL路径字符串），`action`（函数）和可选`children`对象（子路由列表，每个子路由都是路由对象）。 action函数可以返回任何东西 - 一个字符串，一个React组件等。例如：

#### `src/routes/index.js`
```js
export default [
  {
    path: '/tasks',
    action() {
      const resp = await fetch('/api/tasks');
      const data = await resp.json();
      return data && {
        title: `To-do (${data.length})`,
        component: <TodoList {...data} />
      };
    }
  },
  {
    path: '/tasks/:id',
    action({ params }) {
      const resp = await fetch(`/api/tasks/${params.id}`);
      const data = await resp.json();
      return data && {
        title: data.title,
        component: <TodoItem {...data} />
      };
    }
  }
];
```
接下来，实现**URL Matcher**函数，该函数将负责将参数化路径字符串与实际URL进行匹配。 例如，调用`matchURI('/tasks/:id', '/tasks/123')`必须返回`{ id: '123' }`，而调用`matchURI('/tasks/:id', '/foo')`，必须返回`null`。 幸运的是，有一个伟大的库[path-to-regexp](https://github.com/pillarjs/path-to-regexp)，这使得这个任务非常容易。 以下是网址匹配器功能的示例：
#### `src/core/router.js`
```js
import toRegExp from 'path-to-regexp';

function matchURI(path, uri) {
  const keys = [];
  const pattern = toRegExp(path, keys); // TODO: Use caching
  const match = pattern.exec(uri);
  if (!match) return null;
  const params = Object.create(null);
  for (let i = 1; i < match.length; i++) {
    params[keys[i - 1].name] =
      match[i] !== undefined ? match[i] : undefined;
  }
  return params;
}
```
最后，实现一个**Route Resolver**函数，给出一个路由表和一个匹配提供的URL字符串的第一个路由的URL/context，执行它的action方法，如果action方法返回`null`或者`undefined`则返回调用者。 让它应该继续迭代剩余的路由。 如果没有一条路线与提供的URL字符串匹配，则应该抛出异常（未找到）。 以下是这个函数的外观：
#### `src/core/router.js`
```js
import toRegExp from 'path-to-regexp';

function matchURI(path, uri) { ... } // See above

async function resolve(routes, context) {
  for (const route of routes) {
    const uri = context.error ? '/error' : context.pathname;
    const params = matchURI(route.path, uri);
    if (!params) continue;
    const result = await route.action({ ...context, params });
    if (result) return result;
  }
  const error = new Error('Not found');
  error.status = 404;
  throw error;
}

export default { resolve };
```
就这些，下面是一个示例：
```js
import router from './core/router';
import routes from './routes';

router.resolve(routes, { pathname: '/tasks' }).then(result => {
  console.log(result);
  // => { title: 'To-do', component: <TodoList .../> } 
});
```
虽然可以在服务器上使用它，但在浏览器环境中，它必须与客户端导航解决方案相结合。 您可以使用[history](https://github.com/ReactTraining/history) npm模块为您处理此任务。 它是React Router中使用的库，它是[HTML5 History API](https://developer.mozilla.org/docs/Web/API/History_API)的一个包装，可处理与客户端导航相关的所有棘手的浏览器兼容性问题。

首先，创建将初始化`history`模块的新实例的`src/core/history.js`文件，并导出为单例：
#### `src/core/history.js`
```js
import createHistory from 'history/lib/createBrowserHistory';
import useQueries from 'history/lib/useQueries';
export default useQueries(createHistory)();
```
然后插入您的客户端引导代码，如下所示：
#### `src/client.js`
```js
import ReactDOM from 'react-dom';
import history from './core/history';
import router from './core/router';
import routes from './routes';

const container = document.getElementById('root');

function renderRouteOutput({ title, component }) {
  ReactDOM.render(component, container, () => {
    document.title = title;
  });
}

function render(location) {
  router.resolve(routes, location)
    .then(renderRouteOutput)
    .catch(error => router.resolve(routes, { ...location, error })
    .then(renderRouteOutput));
}

render(history.getCurrentLocation()); // render the current URL
history.listen(render);
```
每当一个新位置被推入`history` stack时，`render()`方法将被调用，它本身调用路由器的`resolve()`方法，并将React组件的返回值渲染后加入到DOM。

为了触发客户端导航而不引起全页刷新，您需要使用`history.push()`方法，例如：
```js
import React from 'react';
import history from '../core/history';

class App extends React.Component {
  transition = event => {
    event.preventDefault();
    history.push({
      pathname: event.currentTarget.pathname,
      search: event.currentTarget.search
    });
  };
  render() {
    return (
      <ul>
        <li><a href="/" onClick={this.transition}>Home</a></li>
        <li><a href="/one" onClick={this.transition}>One</a></li>
        <li><a href="/two" onClick={this.transition}>Two</a></li>
      </ul>
    );
  }
}
```
虽然，通常的做法是将转换功能提取到可以使用如下的独立（`Link`）组件中：
```html
<Link to="/tasks/123">View Task #123</Link> 
```
## Routing in React Starter Kit
React Starter Kit (RSK)使用[Universal Router](https://github.com/kriasoft/universal-router) npm模块，该模块围绕之前演示的相同概念构建，主要差异在于它支持嵌套路由，并为您提供了帮助的`Link`React组件。 它可以被看作是React Router的轻量级更灵活的替代方案。

- 它具有最小依赖关系的简单代码（仅需要`path-to-regexp`和`babel-runtime`）
- 它可以与任何JavaScript框架一起使用，如React，Vue.js等
- 它使用与Express和Koa相同的中间件方法，使其易于学习
- 它使用完全相同的API和实现来在Node.js和浏览器环境中使用

"[入门(已翻译)](UniversalRouter.md)"页面有几个如何使用它的示例。

## 相关文章
[You might not need React Router](https://medium.freecodecamp.com/you-might-not-need-react-router-38673620f3d) by Konstantin Tarkus

## 相关项目
[path-to-regexp](https://github.com/pillarjs/path-to-regexp)  
[history](https://github.com/ReactTraining/history)  
[Universal Router](https://github.com/kriasoft/universal-router)  

## 相关讨论
[How to Implement Routing and Navigation](https://github.com/kriasoft/react-starter-kit/issues/748)  
[How to Add a Route to RSK?](https://github.com/kriasoft/react-starter-kit/issues/754)  

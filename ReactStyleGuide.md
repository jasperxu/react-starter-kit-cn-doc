# React 风格指南
> 此风格指南作为[Airbnb React/JSX](https://github.com/airbnb/javascript/tree/master/react)指南的补充。 随意修改它以满足您的项目需求。

## 目录
- 每个UI组件分文件夹
- 推荐使用功能组件
- 使用CSS模块
- 使用高阶组件

## 每个UI组件分文件夹
- 将每个主要的UI组件及其资源放在一个单独的文件夹中。
    
    这将使得更容易找到任何特定UI元素（CSS，images，单元测试，本地化文件等）的相关资源。 在重构过程中移除这些部件也应该很容易。
- 避免在多个组件之间共享CSS，图像和其他资源文件。
    
    这将使您的代码更易维护，易于重构。
- 将`package.json`文件添加到每个组件的文件夹中。

    这将允许从代码中的其他位置轻松引用这些组件。
使用`import Nav from '../Navigation'`，而不是`import Nav from '../Navigation/Navigation.js'`
```
/components/Navigation/icon.svg
/components/Navigation/Navigation.css
/components/Navigation/Navigation.js
/components/Navigation/Navigation.test.js
/components/Navigation/Navigation.ru-RU.css
/components/Navigation/package.json
```
```json
// components/Navigation/package.json
{
  "name:": "Navigation",
  "main": "./Navigation.js"
}
```
有关[基于组件的UI开发](https://google.com/search?q=component-based+ui+development)的更多信息，请访问google。
## 推荐使用功能组件
- 首选使用无状态功能组件。

    不使用状态的组件更好地被写为简单的纯函数。
    
```js
// Bad
class Navigation extends Component {
  static propTypes = { items: PropTypes.array.isRequired };
  render() {
    return <nav><ul>{this.props.items.map(x => <li>{x.text}</li>)}</ul></nav>;
  }
}

// Better
function Navigation({ items }) {
  return (
    <nav><ul>{items.map(x => <li>{x.text}</li>)}</ul></nav>;
  );
}
Navigation.propTypes = { items: PropTypes.array.isRequired };
```

## 使用CSS模块
- 使用CSS模块

  这将允许使用简短的CSS类名称，同时避免冲突。
- 保持CSS简单和声明。 避免循环，混合等
- 随意在CSS中使用变量，通过PostCSS的[precss](https://github.com/jonathantneal/precss)插件
- 推荐CSS类选择器而不是元素和id选择器（见[BEM](https://bem.info/)）
- 避免嵌套CSS选择器（见[BEM](https://bem.info/)）
- 如有疑问，请使用`.root { }`类名称作为组件的根元素

```scss
// Navigation.scss
@import '../variables.scss';

.root {
  width: 300px;
}

.items {
  margin: 0;
  padding: 0;
  list-style-type: none;
  text-align: center;
}

.item {
  display: inline-block;
  vertical-align: top;
}

.link {
  display: block;
  padding: 0 25px;
  outline: 0;
  border: 0;
  color: $default-color;
  text-decoration: none;
  line-height: 25px;
  transition: background-color .3s ease;

  &,
  .items:hover & {
    background: $default-bg-color;
  }

  .selected,
  .items:hover &:hover {
    background: $active-bg-color;
  }
}
```

```js
// Navigation.js
import React from 'react';
import PropTypes from 'prop-types';
import withStyles from 'isomorphic-style-loader/lib/withStyles';
import s from './Navigation.scss';

function Navigation() {
  return (
    <nav className={`${s.root} ${this.props.className}`}>
      <ul className={s.items}>
        <li className={`${s.item} ${s.selected}`}>
          <a className={s.link} href="/products">Products</a>
        </li>
        <li className={s.item}>
          <a className={s.link} href="/services">Services</a>
        </li>
      </ul>
    </nav>
  );
}

Navigation.propTypes = { className: PropTypes.string };

export default withStyles(Navigation, s);
```
## 使用高阶组件
- 使用高阶组件（HOC）扩展现有的React组件。

```js
// withViewport.js
import React, { Component } from 'react';
import { canUseDOM } from 'fbjs/lib/ExecutionEnvironment';

function withViewport(ComposedComponent) {
  return class WithViewport extends Component {

    state = {
      viewport: canUseDOM ?
        {width: window.innerWidth, height: window.innerHeight} :
        {width: 1366, height: 768} // Default size for server-side rendering
    };

    componentDidMount() {
      window.addEventListener('resize', this.handleResize);
      window.addEventListener('orientationchange', this.handleResize);
    }

    componentWillUnmount() {
      window.removeEventListener('resize', this.handleResize);
      window.removeEventListener('orientationchange', this.handleResize);
    }

    handleResize = () => {
      let viewport = {width: window.innerWidth, height: window.innerHeight};
      if (this.state.viewport.width !== viewport.width ||
        this.state.viewport.height !== viewport.height) {
        this.setState({ viewport });
      }
    };

    render() {
      return <ComposedComponent {...this.props} viewport={this.state.viewport}/>;
    }

  };
};

export default withViewport;
```
```js
// MyComponent.js
import React from 'react';
import withViewport from './withViewport';

class MyComponent {
  render() {
    let { width, height } = this.props.viewport;
    return <div>{`Viewport: ${width}x${height}`}</div>;
  }
}

export default withViewport(MyComponent);
```




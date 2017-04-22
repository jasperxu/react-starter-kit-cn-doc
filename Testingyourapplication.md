# 测试应用程序
## 使用库
RSK附带以下库进行测试：
- [Mocha](https://mochajs.org/) - Node.js和浏览器测试运行器
- [Chai](http://chaijs.com/) - 断言库
- [Enzyme](https://github.com/airbnb/enzyme) - React测试工具

您可能还需要查看以下相关软件包：
- [jsdom](https://github.com/tmpvar/jsdom)
- [react-addons-test-utils](https://www.npmjs.com/package/react-addons-test-utils)

## 运行测试
要测试您的应用程序，只需运行`yarn test`命令即可：
- 在`src/`目录中递归查找以`.test.js`结束的所有文件
- mocha执行找到的文件

## 公约
- 测试文件名**必须**以`.test.js`结束，否则`yarn test`将无法检测到它们
- 测试文件名**应该**以相关组件命名（例如，为`Login.js`组件创建`Login.test.js`）

## 基本示例
为了帮助您以RSK的方式提供以下基本测试用例，您可以将其用作起点：
```js
import React from 'react';
import { expect } from 'chai';
import { shallow } from 'enzyme';
import App from '../App';
import Layout from './Layout';

describe('Layout', () => {

  it('renders children correctly', () => {
    const wrapper = shallow(
      <App context={}>
        <Layout>
          <div className="child" />
        </Layout>
      </App>
    );

    expect(wrapper.contains(<div className="child" />)).to.be.true;
  });

});

{ insertCss: () => {} }
```
## React-intl示例
React-intl用户**必须**在IntlProvider内部渲染/包装组件，如下所示：
下面的例子是RSK `Header`组件的一个插件测试：
```js
import React from 'react';
import Header from './Header';
import IntlProvider from 'react-intl';
import Navigation from '../../components/Navigation';

describe('A test suite for <Header />', () => {

  it('should contain a <Navigation/> component', () => {
    it('rendering', () => {
      const wrapper = renderIntoDocument(<IntlProvider locale="en"><Header /></IntlProvider>);
      expect(wrapper.find(Navigation)).to.have.length(1);
    });
  });

});
```
请注意，**不使用**IntlProvider将会产生以下错误：
> Invariant Violation: [React Intl] Could not find required `intl` object. needs to exist in the component ancestry.

## 规范检查
为了检查您的JavaScript和CSS代码是否遵循建议的样式指南运行：
```shell
yarn run lint
```

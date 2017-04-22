# 入门
项目地址：[React Starter Kit (RSK)](https://github.com/kriasoft/react-starter-kit)
## 环境需求
- Mac OS X, Windows, or Linux
- [Yarn](https://yarnpkg.com/) + [Node.js](https://nodejs.org/) v6.5 或更高
- 使用React/JSX/Flow/ESlint预先配置的文本编辑器或IDE（了解更多）

## 目录说明
在开始之前，请花点时间了解项目结构：
```
.
├── /build/                     # 编译输出的文件夹
├── /docs/                      # 项目文档
├── /node_modules/              # 第三方库和实用程序
├── /public/                    # 复制到/build/public文件夹中的静态文件
├── /src/                       # 应用程序的源代码
│   ├── /components/            # React components
│   ├── /core/                  # Core framework and utility functions
│   ├── /data/                  # GraphQL服务架构和数据模型
│   ├── /routes/                # Page/screen组件和路由信息
│   ├── /client.js              # 客户端启动脚本
│   ├── /config.js              # 全局应用设置
│   └── /server.js              # 服务器端启动脚本
├── /test/                      # 单元和端对端测试
├── /tools/                     # 构建自动化脚本和实用程序
│   ├── /lib/                   # Library for utility snippets 实用程序代码片段
│   ├── /build.js               # 将项目源码构建到输出（build）文件夹
│   ├── /bundle.js              # 通过Webpack将Web资源捆绑到包中
│   ├── /clean.js               # 清理输出（build）文件夹
│   ├── /copy.js                # 将静态文件复制到输出（build）文件夹
│   ├── /deploy.js              # 部署您的Web应用程序
│   ├── /postcss.config.js      # 使用PostCSS插件转换样式的配置
│   ├── /run.js                 # 执行构建自动化任务的辅助功能
│   ├── /runServer.js           # 启动（或重新启动）Node.js服务器
│   ├── /start.js               # 使用"live reload"启动开发Web服务器
│   └── /webpack.config.js      # 客户端和服务器端捆绑包的配置
├── Dockerfile                  # 构建Docker映像进行生产的命令
├── package.json                # 第三方库和实用程序的列表
└── yarn.lock                   # 固定版本的所有依赖项
```
注意：当前版本的RSK不包含Flux实现。 它可以轻松地与您选择的任何Flux库集成。 最常用的Flux库是[Flux](http://facebook.github.io/flux/)，[Redux](http://redux.js.org/)和[Relay](http://facebook.github.io/relay/)。

## 快速开始
### 拷贝最后版本
运行如下命令，拷贝[React Starter Kit (RSK)](https://github.com/kriasoft/react-starter-kit)项目到本地。
```shell
$ git clone -o react-starter-kit -b master --single-branch https://github.com/kriasoft/react-starter-kit.git MyApp
$ cd MyApp
```
或者，您可以从[WebStorm IDE](https://www.jetbrains.com/webstorm/help/create-new-project-react-starter-kit.html)或通过使用[Yeoman](https://www.npmjs.com/package/generator-react-fullstack)生成器启动基于RSK的新项目。
### 运行`yarn install`
这将安装package.json文件中列出的运行时项目依赖关系和开发工具。

### 运行`yarn start`
该命令将构建应用程序源文件（`/src`）输出到`/build`文件夹。初始构建完成后，它将启动Node.js服务器（`node build/server.js`），并在其上创建具有[HMR](https://webpack.github.io/docs/hot-module-replacement)的[Browsersync](https://browsersync.io/)。
> http://localhost:3000/ — Node.js server (`build/server.js`)

> http://localhost:3000/graphql — GraphQL server and IDE

> http://localhost:3001/ — BrowserSync proxy with HMR, React Hot Transform

> http://localhost:3002/ — BrowserSync control panel (UI)

现在，您可以在浏览器，移动设备上打开您的网络应用程序，并开始编辑它们。每当您修改`/src`文件夹中的任何源文件时，模块绑定器（[Webpack](http://webpack.github.io/)）将即时重新编译应用程序，并刷新所有连接的浏览器。

注意，`yarn start`命令是在`development`模式下启动应用程序，在这种情况下，编译的输出文件未被优化和最小化。 您可以使用`--release`命令行参数来用`production`模式发布应用程序。
```shell
# 注意这里需要多个减号
yarn start -- --release
```

## 如何构建，测试和部署
### 构建不运行
```shell
# development 模式构建
yarn run build
# production 模式构建
yarn run build -- --release
# production docker 模式构建 
yarn run build -- --release --docker
```
运行此命令后，`/build`文件夹将包含应用程序的编译版本。例如，您可以通过运行`node build/server.js`正常启动Node.js服务器。
### 检查源码语法错误和潜在问题
```shell
yarn run lint
```
### 启动测试单元

```shell
yarn run test          # 用Mocha进行单元测试
yarn run test:watch    # 启动单元测试并开始监控更改
```
默认情况下，[Mocha](https://mochajs.org/)测试运行器寻找与`src/**/*.test.js`模式匹配的测试文件。
例如：`src/components/Layout/Layout.test.js`
### 部署
```shell
yarn run deploy
```
部署脚本`tools/deploy.js`配置为通过Git将`/build`文件夹的内容推送到远程服务器。 您可以轻松地将应用程序部署到Azure Web Apps或Heroku。 两者都将在执行`yarn install --production`时上传你的新文件。 请注意，您应该只将`/build`文件夹的内容部署到远程服务器。

## 如何更新
如果您需要随着最近对RSK进行的更改来保持项目的最新状态，您可以随时通过运行以下方式将其从此回购中取回并合并到自己的项目中：
```shell
$ git checkout master
$ git fetch react-starter-kit
$ git merge react-starter-kit/master
$ yarn install
```


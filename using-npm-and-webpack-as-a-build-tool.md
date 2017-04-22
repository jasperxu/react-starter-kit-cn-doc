# 使用Yarn和Webpack作为构建工具
Node.js附带的[Yarn](https://yarnpkg.com/)命令行实用程序允许您运行任意脚本和Node.js模块，而不需要全局安装。 这是非常方便的，因为您的团队中的其他开发人员无需担心在您的项目中执行构建自动化脚本之前，必须先在全球安装一些工具。

例如，如果您需要使用[ESLint](http://eslint.org/)和[JSCS](http://jscs.info/)对JavaScript代码进行编译，您只需将它们作为项目的依赖项安装：
```shell
$ yarn add eslint jscs --dev
```
向`package.json/scripts`添加新行的命令：
```js
{
  "devDependencies": {
    "eslint": "^1.10.0",
    "jscs": "^2.7.0"
  },
  "scripts": {
    "lint": "eslint src && jscs src"
  }
}
```
并执行它运行：
```shell
$ yarn run lint        # yarn run <script-name>
```
这将与运行`./node_modules/bin/eslint src && ./node_modules/bin/jscs src`相同，除了前者具有较短的语法，并在所有平台上使用相同的方式（Mac OS X，Windows，Linux ）。

以同样的方式，您可以运行[Webpack](http://webpack.github.io/)模块捆绑程序将您的应用程序的源代码编译成可分发的格式。 由于Webpack有许多[配置选项](http://webpack.github.io/docs/configuration)，所以将它们全部放在一个单独的配置文件中是一个好主意，而不是将它们作为命令行参数提供给Webpack的CLI。 根据经验，您希望将`package.json`文件中的"scripts"部分保留得足够短，易于阅读。

例如，您可能有`src/client.js`和`src/server.js`文件作为应用程序的客户端和服务器端代码的入口点。 可以使用以下Webpack配置文件（`webpack.config.js`）将其捆绑到客户端和服务器端应用程序包中：`build/public/client.js`和`build/server.js`：
```js
module.exports = [{
  context: __dirname + '/src'
  entry: './client.js',
  output: {
    path: __dirname + '/build/public',
    filename: 'client.js'
  }
}, {
  context: __dirname + '/src',
  entry: './server.js',
  output: {
    path: __dirname + '/build',
    filename: 'server.js',
    libraryTarget: 'commonjs2'
  },
  target: 'node',
  externals: /node_modules/,
}];
```
它的`npm`脚本可能如下所示：
```shell
{
  "devDependencies": {
    "webpack": "^1.12.0"
  },
  "scripts": {
    "build": "webpack --config webpack.config.js"
  }
}
```
您可以运行如下：
```shell
$ yarn run build
```
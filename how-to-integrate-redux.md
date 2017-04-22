# 如何集成Redux
用Git合并`feature/redux`分支。 如果您对`feature/react-intl`感兴趣，请合并该分支，因为它也包括Redux。

**如果你不太了解Redux，你应该首先[阅读这里](http://redux.js.org/docs/basics/index.html)**。

## 创建Actions

1. 找到`src/constants/index.js`文件，在这里定义action名称。
2. 找到`src/actions/`文件夹，创建具有适当名称的文件。你可以拷贝`src/actions/runtime.js`文件作为一个模板。
3. 如果您需要异步操作，请使用[`redux-thunk`](https://github.com/gaearon/redux-thunk#readme)。 有关如何创建异步操作的问题，您可以从`feature/react-intl`查看[`setLocale`](https://github.com/kriasoft/react-starter-kit/blob/feature/react-intl/src/actions/intl.js)操作。 有关此主题的更多信息，请参阅[Async Flow](http://redux.js.org/docs/advanced/AsyncFlow.html)。


## 创建Reducer(又叫Store)
1. 找到`src/reducers/`文件夹，创建一个新的文件。

    你可以拷贝`src/reducers/runtime.js`文件作为一个模板。
    * 不要忘记总是需要返回`state`。
    * 不要突然提供`state`。 如果您突变状态，则由于`===`相等，渲染连接的组件将不会被触发。 如果执行状态更新，请始终返回新值。 你可以使用这个结构：`{ ...state, updatedKey: action.payload.value, }`
    * 请记住，存储状态_必须_通过重播其上的操作来重复。 例如，当您存储时间戳时，将其传递到_action payload_。 如果您在action中调用了REST API。 **在reducer上永远不要这样做！**
2. 编辑`src/reducers/index.js`，导入您的reducer并将其添加到由[`combineReducers`](http://redux.js.org/docs/api/combineReducers.html)创建的root reducer。

## 连接组件
您可以使用[`react-redux`](https://github.com/reactjs/react-redux#readme)包中的[`connect()`](https://github.com/reactjs/react-redux/blob/master/docs/api.md#connectmapstatetoprops-mapdispatchtoprops-mergeprops-options)高阶组件。

请参阅在redux.js.org上的[使用React](http://redux.js.org/docs/basics/UsageWithReact.html)。

例如，您可以从`feature/react-intl`分支中查看[`<LanguageSwitcher>`](https://github.com/kriasoft/react-starter-kit/blob/feature/react-intl/src/components/LanguageSwitcher/LanguageSwitcher.js)组件。 它展示了订阅存储和调度操作。

## 在服务器上调度操作
See source of `src/server.js`


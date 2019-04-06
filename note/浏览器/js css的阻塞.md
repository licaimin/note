## 动态导入

我们可以使用 Webpack[动态导入](https://webpack.js.org/guides/code-splitting/)来加载应用程序的某些部分。让我们看看它们的工作原理以及它们与常规导入的区别。

标准的 JS 模块导入：

```
// main.js
import ModuleA from './module_a.js'
ModuleA.doStuff()
```

它将作为 main.js 的叶子被添加到依赖图中，并被捆绑到捆绑包中。

但是，如果我们仅在某些情况下需要 ModuleA 呢？将这个模块与==初始捆绑包==捆绑在一起不是一个好主意，因为可能根本就不需要它。我们需要一种方法来告诉应用程序应该在什么时候下载这段代码。

这个时候可以使用动态导入！来看一下这个例子：



```
//main.js
const getModuleA = () => import('./module_a.js')
// invoked as a response to some user interaction
getModuleA()
  .then({ doStuff } => doStuff())
```

我们来看看这里都发生了什么：

我们创建了一个==返回 import() 函数的函数==，而不是直接导入 module_a.js。现在 Webpack 会将动态导入模块的内容捆绑到一个单独的文件中，==除非调用了这个函数，否则 import() 也不会被调用==，也就不会下载这个文件。在后面的代码中，我们下载了这个可选的代码块，作为对某些用户交互的响应。

通过使用动态导入，我们基本上隔离了将被添加到依赖图中的叶子（在这里是 module_a），并在需要时下载它（这意味着我们也切断了在 module_a.js 中导入的模块）。

# Vue异步组件

在大型应用中，我们可能需要将应用分割成小一些的代码块，并且只在需要的时候才从服务器加载一个模块。为了简化，Vue 允许你以==一个工厂函数的方式定义你的组件==，这个工厂函数会==异步==解析你的组件定义。Vue 只有在这个组件需要被渲染的时候才会触发该工厂函数，且会把==结果缓存起来==供未来重渲染。例如：

```
Vue.component('async-example', function (resolve, reject) {
  setTimeout(function () {
    // 向 `resolve` 回调传递组件定义
    resolve({
      template: '<div>I am async!</div>'
    })
  }, 1000)
})
```

如你所见，这个工厂函数会收到一个 `resolve` 回调，这个回调函数会在你从服务器得到组件定义的时候被调用。你也可以调用 `reject(reason)` 来表示加载失败。这里的 `setTimeout` 是为了演示用的，如何获取组件取决于你自己。一个推荐的做法是将异步组件和 [webpack 的 code-splitting 功能](https://webpack.js.org/guides/code-splitting/)一起配合使用：

```
Vue.component('async-webpack-example', function (resolve) {
  // 这个特殊的 `require` 语法将会告诉 webpack
  // 自动将你的构建代码切割成多个包，这些包
  // 会通过 Ajax 请求加载
  require(['./my-async-component'], resolve)
})
```

你也可以在工厂函数中返回一个 `Promise`，所以把 webpack 2 和 ES2015 语法加在一起，我们可以写成这样：

```
Vue.component(
  'async-webpack-example',
  // 这个 `import` 函数会返回一个 `Promise` 对象。
  () => import('./my-async-component')
)
```

当使用[局部注册](https://cn.vuejs.org/v2/guide/components-registration.html#%E5%B1%80%E9%83%A8%E6%B3%A8%E5%86%8C)的时候，你也可以直接提供一个返回 `Promise` 的函数：

```
new Vue({
  // ...
  components: {
    'my-component': () => import('./my-async-component')
  }
})
```

如果你是一个 **Browserify** 用户同时喜欢使用异步组件，很不幸这个工具的作者[明确表示](https://github.com/substack/node-browserify/issues/58#issuecomment-21978224)异步加载“并不会被 Browserify 支持”，至少官方不会。Browserify 社区已经找到了[一些变通方案](https://github.com/vuejs/vuejs.org/issues/620)，这些方案可能会对已存在的复杂应用有帮助。对于其它的场景，我们推荐直接使用 webpack，以拥有内置的头等异步支持。


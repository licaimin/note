在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM。

## 异步说明

> Vue 实现响应式并**不是数据发生变化之后 DOM 立即变化**，而是按一定的策略进行 DOM 的更新

https://github.com/DDFE/DDFE-blog/issues/24

我们在有之前的知识背景，再理解nextTick的实现就不难了，这里有一段很关键的注释：在Vue 2.4之前的版本，nextTick几乎都是基于微任务实现的，但由于微任务的执行优先级非常高，在某些场景下它甚至要比事件冒泡还要快，就会导致一些诡异的问题，如果[＃4521](https://github.com/vuejs/vue/issues/4521)，[＃6690](https://github.com/vuejs/vue/issues/6690)，[＃6566](https://github.com/vuejs/vue/issues/6566) ;但是如果全部都改成宏任务，对一些有重绘和动画的场景也会有性能影响，如问题[＃6813](https://github.com/vuejs/vue/issues/6813)。所以最终nextTick采取的策略是默认走微任务，对于一些DOM交互事件，如v-on绑定的事件回调函数的处理，会强制走宏任务。

这个强制是怎么做的呢，原来在Vue.js在绑定DOM事件的时候，默认会给回调的`handler`函数调用`withMacroTask`方法做一层包装，它保护整个回调函数执行过程中，遇到数据状态的改变，这些改变都会被推到了宏任务中。

对于宏任务的执行，Vue.js优先检测是否支持原生`setImmediate`，这是一个高版本IE和Edge才支持的特性，不支持的话再去检测是否支持原生的`MessageChannel`，如果也不支持的话就会降级为`setTimeout 0`。


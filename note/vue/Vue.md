# v-show v-if
v-if 指令用于条件性地渲染一块内容。这块内容只会在指令的表达式返回 truthy 值的时候被渲染
相当于注释   
带有 v-show 的元素始终会被渲染并保留在 DOM 中。v-show 只是简单地切换元素的 CSS 属性 display    
一般来说，v-if 有更高的切换开销，而 v-show 有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用 v-show 较好；如果在运行时条件很少改变，则使用 v-if 较好
## 组件注册
```
components: {
    'component-a': ComponentA,
    'component-b': ComponentB
  }
```
# 父子组件通信
https://www.cnblogs.com/sichaoyun/p/6690322.html
父组件是通过props 属性传给子组件
子组件是通过$emit 属性 传给父组件

## 单向数据流
所有的 prop 都使得其父子 prop 之间形成了一个单向下行绑定：父级 prop 的更新会向下流动到子组件中，但是反过来则不行。这样会防止从子组件意外改变父级组件的状态，从而导致你的应用的数据流向难以理解。
## 自定义事件   kebab-case 的事件名
不同于组件和 prop，事件名不存在任何自动化的大小写转换。
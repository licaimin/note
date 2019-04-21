首先看个例子

```
new  Vue({
    el: '#app',
    template: `<div>{{demo}}</div>`,
    data: {
        demo: 123
    }
})
```

在new vue()中，data可以直接是一个对象，为什么在vue组件中，data必须是一个函数呢？

我们大致可以通过`js原型链`来对比下：

```
var Component = function() {};
Component.prototype.data = {
    demo: 123
}
var component1 = new Component();
var component2 = new Component();
component1.data.demo = 456;
console.log(component2.data.demo); // 456
```

从上面可以看出，两个实例都引用同一个对象，其中一个改变的时候，另一个也发生改变。

每一个vue组件都是一个vue实例，通过new Vue()实例化，引用同一个对象，如果data直接是一个对象的话，那么一旦修改其中一个组件的数据，其他组件相同数据就会被改变。

而data是函数的话，每个vue组件的data都因为==函数有了自己的作用域==，互不干扰。


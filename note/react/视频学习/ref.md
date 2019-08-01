ref

```
   this.setState((preState)=>({
      list:[...preState.list,preState.inputValue],
      inputValue:''
    }))
    console.log(this.ul.querySelectorAll('div').length)
```

发现添加元素后，对应的div的length没有跟着变化，因为setState是异步函数

那如何获取更新后的dom呢？

```
 this.setState((preState)=>({
      list:[...preState.list,preState.inputValue],
      inputValue:''
    }),()=>{
      console.log(this.ul.querySelectorAll('div').length)
    })
```

setState的第二个参数就可以获取更新后的DOM

# 生命周期

## render

#### 一、初始化阶段

##### 1、设置组件的默认属性

```
static defaultProps = {
    name: 'sls',
    age:23
};
//or
Counter.defaltProps={name:'sls'}
复制代码
```

##### 2、设置组件的初始化状态

```
constructor() {
    super();
    this.state = {number: 0}
}
复制代码
```

##### 3、componentWillMount()

> 组件即将被渲染到页面之前触发，此时可以进行开启定时器、向服务器发送请求等操作

##### 4、render()

> 组件渲染

##### 5、componentDidMount()

> 组件已经被渲染到页面中后触发：此时页面中有了真正的DOM的元素，可以进行DOM相关的操作

==以上只会第一次组件（除了render）挂载在页面上才会执行，之后就是更新阶段了==

#### 二、运行中阶段

##### 1、componentWillReceiveProps()

> 组件接收到属性时触发

在子组件写这个生命周期什么时候开始执行呢？

1.一个组件要从父组件接受参数

2.如果这个组件第一次存在于父组件中，不会执行

3.如果这个组件内之前已经存在于父组件中，才会执行

```
例子：一个有输入框的添加列表，列表的内容为组件，父组件传递input的value给子组件，第一次输入时然后提交，并不会触发。接受在input输入内容（只是输入）就触发了
```

> 生命周期阶段

```
shouldComponentUpdate
componentWillUpdate
render
child componentWillReceiveProps
componentDidUpdate
```



##### 2、shouldComponentUpdate()

==要有返回值，即这个组件需要更新吗==

> 当组件接收到新属性，或者组件的状态发生改变时触发。组件首次渲染时并不会触发
>
> ```
> shouldComponentUpdate
> TodoList.js:20 componentWillUpdate
> TodoList.js:26 render
> TodoList.js:23 componentDidUpdate
> ```
>
> 

```
shouldComponentUpdate(newProps, newState) {
    if (newProps.number < 5) return true;
    return false
}
//该钩子函数可以接收到两个参数，新的属性和状态，返回true/false来控制组件是否需要更新。
复制代码
```

> 一般我们通过该函数来优化性能：

> 一个React项目需要更新一个小组件时，很可能需要父组件更新自己的状态。而一个父组件的重新更新会造成它旗下所有的子组件重新执行render()方法，形成新的虚拟DOM，再用diff算法对新旧虚拟DOM进行结构和属性的比较，决定组件是否需要重新渲染

> 无疑这样的操作会造成很多的性能浪费，所以我们开发者可以根据项目的业务逻辑，在`shouldComponentUpdate()`中加入条件判断，从而优化性能

> 例如React中的就提供了一个`PureComponent`的类，当我们的组件继承于它时，组件更新时就会默认先比较新旧属性和状态，从而决定组件是否更新。值得注意的是，`PureComponent`进行的是浅比较，所以组件状态或属性改变时，都需要返回一个新的对象或数组

##### 3、componentWillUpdate()

> 组件即将被更新时触发

##### 4、componentDidUpdate()

==在render后==

> 组件被更新完成后触发。页面中产生了新的DOM的元素，可以进行DOM操作

#### 三、销毁阶段

##### 1、componentWillUnmount()

> 组件即将？被销毁时触发。这里我们可以进行一些清理操作，例如清理定时器，取消Redux的订阅事件等等。
>
> 子组件被移除，生命周期过程：

```
shouldComponentUpdate
 componentWillUpdate
 render
 child componentWillUnmount
componentDidUpdate
```

## 性能提升

### 1. shouldComponentUpdate()

减少虚拟DOM的diff过程

```
shouldComponentUpdate(nextProps,nextState){
    console.log('next',nextProps.content)
    console.log('this',this.props.constent)
    if(nextProps.content !== this.props.content){
      return true
    }else{
      return false
    }

  }
```

[react文档](http://react.html.cn/docs/optimizing-performance.html)

# charles数据模拟

# react-transition-group


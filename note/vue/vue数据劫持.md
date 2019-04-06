
## observer 监听器：用来监听属性的变化通知订阅者
## Watcher 订阅者：收到属性的变化，然后更新视图
## Compile 解析器：解析指令，初始化模版，绑定订阅者

Object.defineProperty是一个无法被shim的属性，就是说它无法被降级使用，这也是vuejs不支持ie8以下的根本原因。

## Object.defineProperty用来定义新属性或修改原有的属性。并返回该对象

，关键是在设置属性的同时，可以设置setter/getter，
setter/getter设置两个函数，在这个属性被调用或者设置的时候自动执行，所以在setter的函数里，只要写了更新dom的方法，就可以在这个属性变化的时候执行，实现了属性变化的追踪。
即
 劫持了对对象赋值的过程，在这个过程中可以进行更新 dom 操作等等。

 Vue会把 prop data等变成响应式对象，在创建过程中，发现子属性也为对象，则递归把该对象变成响应式（即拥有get ,set, __ob__属性）

 ![image](https://segmentfault.com/img/bV9lca?w=1200&h=750)

 vuejs里每一个组件对应了一个watcher，Object.defineProperty是紫色的圆圈，当组件里某一个属性被get的时候，getter函数会通知Watcher，“说我这有一个属性被渲染了，你记一下”，然后当这个属性的setter被触发（也就是该属性数据被修改的时候），也会通知Watcher，说“我这有这样一个东西被改了，你看看在不在你的名单里。”Watcher此时去检查被改的属性在不在自己记录的名单里，如果在，就通知组件渲染程序，


 ### 超级简单的例子
 ```
     <input id="inputValue">
        <p id="textValue"> </p>
        <p > ss</p>


        <input type="text" id="input" />
        <span id="txt"></span>
    <script>
        var inputvalue = document.getElementById('inputValue');
        var textValue = document.getElementById('textValue');
        var obj={};
        Object.defineProperty(obj,'change',{
            set:function (value) {
                textValue.innerHTML = value;
                inputvalue.value = value;
            }
        });
        inputvalue.addEventListener('click',function (e) {
            obj.change = e.target.value;
        })
 ```
 不过注意是要点击才会触发
 实现了model-view 
 控制台输入 obj.change = 'ss', input 跟 p 都会输出 'ss'

 ## 需要注意的几个点：
1.getter/setter对用户是不可见的，是在vue内部实现的。
2.js里无法监听对象属性的增加或者删除，所以vue只能在开始data里添加响应式属性，所以当组件创建完毕，再给这个组件塞一个属性，这个属性是无法响应到dom的。
3.vue会在组件初始化的过程中进行getter/setter转换，所以也无法动态插入新属性，插入了也是非响应数据，但可以通过Vue.set(object, key, value)方法将属性加入到后台可响应的对象中。
4.官网还介绍了更新队列，上文说的Watcher中的更新会被推入到一个更新队列中，那么就是说数据更新后不会马上反映到dom上。
5.但是我们可以通过Vue.nextTick(callback)方法，将这次数据更新马上反映到dom上，这个方法的callback是dom更新完成的回调。
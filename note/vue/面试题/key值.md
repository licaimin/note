https://juejin.im/post/5aae19aa6fb9a028d4445d1a

使用`v-for`更新已渲染的元素列表时,默认用`就地复用`策略;列表数据修改的时候,他会根据key值去判断某个值是否修改,如果修改,则重新渲染这一项,否则复用之前的元素; 我们在使用的使用经常会使用`index`(即数组的下标)来作为`key`,但其实这是不推荐的一种使用方法;

举个🌰

```
const list = [
    {
        id: 1,
        name: 'test1',
    },
    {
        id: 2,
        name: 'test2',
    },
    {
        id: 3,
        name: 'test3',
    },
]
复制代码
<div v-for="(item, index) in list" :key="index" >{{item.name}}</div>
复制代码
```

上面这种是我们做项目中常用到的一种场景,因为不加key,vue现在直接报错,所以我使用index作为key;下面列举两种常见的数据更新情况

### 1.在最后一条数据后再加一条数据

```
const list = [
    {
        id: 1,
        name: 'test1',
    },
    {
        id: 2,
        name: 'test2',
    },
    {
        id: 3,
        name: 'test3',
    },
    {
        id: 4,
        name: '我是在最后添加的一条数据',
    },
]
复制代码
```

此时前三条数据直接复用之前的,新渲染最后一条数据,此时用`index`作为`key`,没有任何问题;

### 2.在中间插入一条数据

```
const list = [
    {
        id: 1,
        name: 'test1',
    },
    {
        id: 4,
        name: '我是插队的那条数据',
    }
    {
        id: 2,
        name: 'test2',
    },
    {
        id: 3,
        name: 'test3',
    },
]
复制代码
```

此时更新渲染数据,通过`index`定义的`key`去进行前后数据的对比,发现

```
之前的数据                         之后的数据

key: 0  index: 0 name: test1     key: 0  index: 0 name: test1
key: 1  index: 1 name: test2     key: 1  index: 1 name: 我是插队的那条数据
key: 2  index: 2 name: test3     key: 2  index: 2 name: test2
                                                 key: 3  index: 3 name: test3
复制代码
```

通过上面清晰的对比,发现除了第一个数据可以复用之前的之外,另外三条数据都需要重新渲染;

是不是很惊奇,我明明只是插入了一条数据,怎么三条数据都要重新渲染?而我想要的只是新增的那一条数据新渲染出来就行了

最好的办法是使用数组中不会变化的那一项作为`key`值,对应到项目中,即每条数据都有一个唯一的`id`,来标识这条数据的唯一性;使用`id`作为`key`值,我们再来对比一下向中间插入一条数据,此时会怎么去渲染

```
之前的数据                         之后的数据

key: 1  id: 1 index: 0 name: test1     key: 1  id: 1 index: 0  name: test1
key: 2  id: 2 index: 1 name: test2     key: 4  id: 4 index: 1  name: 我是插队的那条数据
key: 3  id: 3 index: 2 name: test3     key: 2  id: 2 index: 2  name: test2
                                                         key: 3  id: 3 index: 3  name: test3
复制代码
```

现在对比发现只有一条数据变化了,就是`id`为4的那条数据,因此只要新渲染这一条数据就可以了,其他都是就复用之前的;

同理在react中使用map渲染列表时,也是必须加key,且推荐做法也是使用`id`,也是这个原因;

其实,真正的原因并不是vue和react怎么怎么,而是因为Virtual DOM 使用Diff算法实现的原因,

> 下面大致从虚拟DOM的Diff算法实现的角度去解释一下

vue和react的虚拟DOM的Diff算法大致相同，其核心是基于两个简单的假设：

1. 两个相同的组件产生类似的DOM结构，不同的组件产生不同的DOM结构。
2. 同一层级的一组节点，他们可以通过唯一的id进行区分。基于以上这两点假设，使得虚拟DOM的Diff算法的复杂度从O(n^3)降到了O(n)。

引用[React’s diff algorithm](https://link.juejin.im?target=https%3A%2F%2Fcalendar.perfplanet.com%2F2013%2Fdiff%2F)中的例子:



![diff1.jpg](https://user-gold-cdn.xitu.io/2018/6/15/16401a8ca6bea0ca?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

 当某一层有很多相同的节点时，也就是列表节点时，Diff算法的更新过程默认情况下也是遵循以上原则。 比如一下这个情况： 

![diff2.jpg](https://user-gold-cdn.xitu.io/2018/6/15/16401a8ca6f8c764?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

 我们希望可以在B和C之间加一个F，Diff算法默认执行起来是这样的： 

![diff3.jpg](https://user-gold-cdn.xitu.io/2018/6/15/16401a8ca73549bb?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

 即把C更新成F，D更新成C，E更新成D，最后再插入E，是不是很没有效率？



所以我们需要使用key来给每个节点做一个唯一标识，Diff算法就可以正确的识别此节点，找到正确的位置区插入新的节点。 

![diff4.jpg](https://user-gold-cdn.xitu.io/2018/6/15/16401a8ca6d7e50c?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



所以一句话，key的作用主要是为了高效的更新虚拟DOM。另外vue中在使用相同标签名元素的过渡切换时，也会使用到key属性，其目的也是为了让vue可以区分它们，否则vue只会替换其内部属性而不会触发过渡效果。
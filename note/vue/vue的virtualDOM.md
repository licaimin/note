[如果自己写的还是不太懂，看这个](https://juejin.im/entry/589d2357b123db16a3c76388)

## VirtualDOM
 Virtual DOM 就是用一个原生的 JS 对象去描述一个 DOM 节点，所以它比创建一个 DOM 的代价要小很多。

### 2. virtual DOM和真实DOM的区别？
```

virtual DOM是将真实的DOM的数据抽取出来，以对象的形式模拟树形结构。比如dom是这样的：
<div>
    <p>123</p>
</div>
复制代码对应的virtual DOM（伪代码）：
var Vnode = {
    tag: 'div',
    children: [
        { tag: 'p', text: '123' }
    ]
};

```


## Vnode
在 Vue.js 中，Virtual DOM 是用 VNode 这么一个 Class 去描述，它是定义在 src/core/vdom/vnode.js 中的。
```
export default class VNode {
  tag: string | void;
  data: VNodeData | void;
  children: ?Array<VNode>;
  text: string | void;
  elm: Node | void;
  ns: string | void;
  context: Component | void; // rendered in this component's scope
  key: string | number | void;
  componentOptions: VNodeComponentOptions | void;
  componentInstance: Component | void; // component instance
  parent: VNode | void; // component placeholder node

```
解释
```
tag: 当前节点的标签名

data: 当前节点的数据对象，具体包含哪些字段可以参考vue源码types/vnode.d.ts中对VNodeData的定义
children: 数组类型，包含了当前节点的子节点

text: 当前节点的文本，一般文本节点或注释节点会有该属性

elm: 当前虚拟节点对应的真实的dom节点

ns: 节点的namespace

context: 编译作用域


functionalContext: 函数化组件的作用域

key: 节点的key属性，用于作为节点的标识，有利于patch的优化

componentOptions: 创建组件实例时会用到的选项信息

child: 当前节点对应的组件实例

parent: 组件的占位节点

raw: raw html

isStatic: 静态节点的标识

isRootInsert: 是否作为根节点插入，被<transition>包裹的节点，该属性的值为false

isComment: 当前节点是否是注释节点

isCloned: 当前节点是否为克隆节点

isOnce: 当前节点是否有v-once指令
```
#### 补充下vnodeData在 flow/vnode.js 
(tag class on show)
```
declare interface VNodeData {
  key?: string | number;
  slot?: string;
  ref?: string;
  is?: string;
  pre?: boolean;
  tag?: string;
  staticClass?: string;
  class?: any;
  staticStyle?: { [key: string]: any };
  style?: Array<Object> | Object;
  normalizedStyle?: Object;
  props?: { [key: string]: any };
  attrs?: { [key: string]: string };
  domProps?: { [key: string]: any };
  hook?: { [key: string]: Function };
  on?: ?{ [key: string]: Function | Array<Function> };
  nativeOn?: { [key: string]: Function | Array<Function> };
  transition?: Object;
  show?: boolean; // marker for v-show

  };
};

```

##  createElement
[createElement的解析](https://juejin.im/entry/589d2357b123db16a3c76388) 


Virtual DOM 除了它的数据结构的定义，映射到真实的 DOM 实际上要经历 VNode 的 ==create、diff、patch== 等过程。那么在 Vue.js 中，VNode 的 create 是通过之前提到的 createElement 方法创建的，我们接下来分析这部分的实现

_createElement 方法有 5 个参数，context 表示 VNode 的上下文环境，它是 Component 类型；tag 表示标签，它可以是一个字符串，也可以是一个 Component；data 表示 VNode 的数据，它是一个 VNodeData 类型，可以在 flow/vnode.js 中找到它的定义，这里先不展开说；children 表示当前 VNode 的子节点，它是任意类型的，它接下来需要被规范为标准的 VNode 数组；normalizationType 表示子节点规范的类型，类型不同规范的方法也就不一样，它主要是参考 render 函数是编译生成的还是用户手写的。

![image](https://user-gold-cdn.xitu.io/2017/2/10/b758cc95753198385e1b7d2a424eebd3?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)
VNode可以理解为vue框架的虚拟dom的基类，通过new实例化的VNode大致可以分为几类
```
EmptyVNode: 没有内容的注释节点

TextVNode: 文本节点

ElementVNode: 普通元素节点

ComponentVNode: 组件节点

CloneVNode: 克隆节点，可以是以上任意类型的节点，唯一的区别在于isCloned属性为true
```

## patch  函数在vdom.patch.js
patch函数接收4个参数：

oldVnode: 旧的虚拟节点或旧的真实dom节点

vnode: 新的虚拟节点

hydrating: 是否要跟真是dom混合

removeOnly: 特殊flag，用于<transition-group>组件


来看看patch是怎么打补丁的（代码只保留核心部分）
```
function patch (oldVnode, vnode) {
    // some code
    if (sameVnode(oldVnode, vnode)) {
    	patchVnode(oldVnode, vnode)
    } else {
    	const oEl = oldVnode.el // 当前oldVnode对应的真实元素节点
    	let parentEle = api.parentNode(oEl)  // 父元素
    	createEle(vnode)  // 根据Vnode生成新元素
    	if (parentEle !== null) {
            api.insertBefore(parentEle, vnode.el, api.nextSibling(oEl)) // 将新元素添加进父元素
            api.removeChild(parentEle, oldVnode.el)  // 移除以前的旧元素节点
            oldVnode = null
    	}
    }
    // some code 
    return vnode
}
```

### patch的策略是：

1. 如果vnode不存在但是oldVnode存在，说明意图是要销毁老节点，那么就调用==invokeDestroyHook(oldVnode)==来进行销毁
 ```
 
  function invokeDestroyHook (vnode) {
    let i, j
    const data = vnode.data
    if (isDef(data)) {
      if (isDef(i = data.hook) && isDef(i = i.destroy)) i(vnode)
      for (i = 0; i < cbs.destroy.length; ++i) cbs.destroy[i](vnode)
    }
    if (isDef(i = vnode.children)) {
      for (j = 0; j < vnode.children.length; ++j) {
        invokeDestroyHook(vnode.children[j])
      }
    }
  }
 ```

2. 如果oldVnode不存在但是vnode存在，说明意图是要创建新节点，那么就调用==createElm==来创建新节点
```
function createElm (
    vnode,
    insertedVnodeQueue,
    parentElm,
    refElm,
    nested,
    ownerArray,
    index
  ) {
    if (isDef(vnode.elm) && isDef(ownerArray)) {
```
 3.当vnode和oldVnode都存在时

 3.1 如果oldVnode和vnode是同一个节点
 （如果两个节点都是一样的，那么就深入检查他们的子节点。）
 ，就调用==patchVnode==来进行patch
```
function sameVnode (a, b) {
  return (
    a.key === b.key &&  // key值
    a.tag === b.tag &&  // 标签名
    a.isComment === b.isComment &&  // 是否为注释节点
    // 是否都定义了data，data包含一些具体信息，例如onclick , style
    isDef(a.data) === isDef(b.data) &&  
    sameInputType(a, b) // 当标签是<input>的时候，type必须相同
  )
}

```
3.2 当vnode和oldVnode不是同一个节点时，不值得比较则==用Vnode替换oldVnode==

==虽然这两个节点不一样但是他们的子节点一样怎么办？别忘了，diff可是逐层比较的，如果第一层不一样那么就不会继续深入比较第二层了==

如果oldVnode是真实dom节点或hydrating设置为true，需要用hydrate函数将虚拟dom和真是dom进行映射，然后将oldVnode设置为对应的虚拟dom，找到oldVnode.elm的父节点，根据vnode创建一个真实dom节点并插入到该父节点中oldVnode.elm的位置

## 3.1 patchVnode算法是：

3.1.1.如果oldVnode跟vnode完全一致，那么不需要做任何事情
```
 {
    if (oldVnode === vnode) {
      return
    }
```
3.2.2.如果oldVnode跟vnode都是静态节点，且具有相同的key，当vnode是克隆节点或是v-once指令控制的节点时，只需要把oldVnode.elm和oldVnode.child都复制到vnode上，也不用再有其他操作
```
 if (isTrue(vnode.isStatic) &&
      isTrue(oldVnode.isStatic) &&
      vnode.key === oldVnode.key &&
      (isTrue(vnode.isCloned) || isTrue(vnode.isOnce))
    ) {
      vnode.componentInstance = oldVnode.componentInstance
      return
    }
```

3.3.3.否则，如果vnode不是文本节点或注释节点
```
if (isUndef(vnode.text)) {

            if (isDef(oldCh) && isDef(ch)) {
                //如果oldVnode和vnode都有子节点，且2方的子节点不完全一致，就执行更新子节点的操作（这一部分其实是在updateChildren函数中实现），
                if (oldCh !== ch) updateChildren(elm, oldCh, ch, insertedVnodeQueue, removeOnly)
        
            } else if (isDef(ch)) {
                        //如果只有vnode有子节点，那就创建这些子节点
                        if (process.env.NODE_ENV !== 'production') {
                            checkDuplicateKeys(ch)
                        }
                        if (isDef(oldVnode.text)) nodeOps.setTextContent(elm, '')
                        addVnodes(elm, null, ch, 0, ch.length - 1, insertedVnodeQueue)
            } else if (isDef(oldCh)) {
                        //如果只有oldVnode有子节点，那就把这些节点都删除
                        removeVnodes(elm, oldCh, 0, oldCh.length - 1)
            } else if (isDef(oldVnode.text)) {
               // 如果oldVnode和vnode都没有子节点，但是oldVnode是文本节点或注释节点，就把vnode.elm的文本设置为空字符串
                        nodeOps.setTextContent(elm, '')
            }
}

```
3.4.4.  如果vnode是文本节点或注释节点，但是vnode.text != oldVnode.text时，只需要更新vnode.elm的文本内容就可以
```
 else if (oldVnode.text !== vnode.text) {
      
        nodeOps.setTextContent(elm, vnode.text)
}
```

### updateChildren 真正的diff算法在这里

####  diff的比较方式？
在采取diff算法比较新旧节点的时候，比较只会在同层级进行, 不会跨层级比较。
```
<div>
    <p>123</p>
</div>

<div>
    <span>456</span>
</div>
```
复制代码上面的代码会分别比较同一层的两个div以及第二层的p和span，但是不会拿div和span作比较。在别处看到的一张很形象的图：
![image](https://user-gold-cdn.xitu.io/2018/5/19/163776ba7bda2d47?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)
### diff流程图
当数据发生改变时，set方法会让调用Dep.notify通知所有订阅者Watcher，订阅者就会调用patch给真实的DOM打补丁，更新相应的视图。
![image](https://user-gold-cdn.xitu.io/2018/5/19/163777930be304eb?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

在开始分析 diff 之前，我们要清楚地知道一件事情，==Snabbdom== 中的 vnode 的 patch 只会发生在同一层级，不会跨层级进行

这是因为，如果需要跨层级进行比对，diff 算法的时间复杂度可达到== O(n^3)==

### 为啥时间复杂度是 O(n^3)：

我们可以把 diff 算法看作要合并两棵树 A 和 B 然后生成一棵新的树，那么要首先寻找子树, 从 A 树的根节点开始，然后从 B 树的根节点找起，同时遍历两棵树，看看两者是否相同，如果有不一样的，那么从 B 树的根节点的子节点再次遍历比较。假设 B 树的节点数为 N， 那么复杂度为 N^2)。假设 A 树的节点数为 M，那么遍历树 A 和树 B 的复杂度为 M * N^2，假设 M 约等于 N，那么总的复杂度为 O(N^3)

这样的算法性能是无法接受的，所以只进行同层比较的话，diff 算法的时间复杂度就变为了 O(n)，相对来说也比较高效了

假设我们现在从根节点开始 diff, 如下图所示：

![image](https://macsalvation.net/2018/08/06/dive-deep-into-vue-part-1-vdom-and-diff/diff-start.png)
这个算法会给新旧 vnode 的 children 数组同时提供头尾两个指针

diff 的过程中，头尾的指针不断地向中间移动，直到有任意一方的 chilren 头指针超过尾指针，diff 结束

##  3.3.3updateChildren开始diff


[具体看这篇文章](https://macsalvation.net/2018/08/06/dive-deep-into-vue-part-1-vdom-and-diff/)


[关于diff的图解](https://cloud.tencent.com/developer/article/1006029)

先说下下这个函数做了什么

将Vnode的子节点Vch和oldVnode的子节点oldCh提取出来
oldCh和vCh各有两个头尾的变量StartIdx和EndIdx，它们的2个变量相互比较，一共有4种比较方式。如果4种比较都没匹配，如果设置了key，就会用key进行比较，在比较的过程中，变量会往中间靠，一旦StartIdx>EndIdx表明oldCh和vCh至少有一个已经遍历完了，就会结束比较。


1.分别获取oldVnode和vnode的firstChild、lastChild，赋值给oldStartVnode、oldEndVnode、newStartVnode、newEndVnode
```
function updateChildren (parentElm, oldCh, newCh, insertedVnodeQueue, removeOnly) {
    let oldStartIdx = 0
    let newStartIdx = 0
    let oldEndIdx = oldCh.length - 1
    let oldStartVnode = oldCh[0]
    let oldEndVnode = oldCh[oldEndIdx]
    let newEndIdx = newCh.length - 1
    let newStartVnode = newCh[0]
    let newEndVnode = newCh[newEndIdx]
    let oldKeyToIdx, idxInOld, vnodeToMove, refElm

//isUndef  第五种情况该节点设为undefined
function updateChildren (parentElm, oldCh, newCh, insertedVnodeQueue, removeOnly) {
    //分别获取oldVnode和vnode的firstChild、lastChild，赋值给oldStartVnode、oldEndVnode、newStartVnode、newEndVnode
    let oldStartIdx = 0
    let newStartIdx = 0
    let oldEndIdx = oldCh.length - 1
    let oldStartVnode = oldCh[0]
    let oldEndVnode = oldCh[oldEndIdx]
    let newEndIdx = newCh.length - 1
    let newStartVnode = newCh[0]
    let newEndVnode = newCh[newEndIdx]
    let oldKeyToIdx, idxInOld, vnodeToMove, refElm

    // removeOnly is a special flag used only by <transition-group>
    // to ensure removed elements stay in correct relative positions
    // during leaving transitions
    const canMove = !removeOnly

    if (process.env.NODE_ENV !== 'production') {
        checkDuplicateKeys(newCh)
    }

    while (oldStartIdx <= oldEndIdx && newStartIdx <= newEndIdx) {

        if (isUndef(oldStartVnode)) {//第五种情况，如果为null,及是新node里没有，可以忽略，最后再删除
            oldStartVnode = oldCh[++oldStartIdx] // Vnode has been moved left

        } else if (isUndef(oldEndVnode)) {// 第五种情况，如果为null,及是新node里没有，可以忽略，最后再删除
            oldEndVnode = oldCh[--oldEndIdx]

        } else if (sameVnode(oldStartVnode, newStartVnode)) {//第一种情况如果oldStartVnode和newStartVnode是同一节点，
            // 调用patchVnode进行patch，然后将oldStartVnode和newStartVnode都设置为下一个子节点，重复上述流程
            patchVnode(oldStartVnode, newStartVnode, insertedVnodeQueue, newCh, newStartIdx)
            oldStartVnode = oldCh[++oldStartIdx]
            newStartVnode = newCh[++newStartIdx]

        } else if (sameVnode(oldEndVnode, newEndVnode)) {//第二种情况如果oldEndVnode和newEndVnode是同一节点，
            // 调用patchVnode进行patch，然后将oldEndVnode和newEndVnode都设置为上一个子节点，重复上述流程
            patchVnode(oldEndVnode, newEndVnode, insertedVnodeQueue, newCh, newEndIdx)
            oldEndVnode = oldCh[--oldEndIdx]
            newEndVnode = newCh[--newEndIdx]

        } else if (sameVnode(oldStartVnode, newEndVnode)) { // Vnode moved right
        //第三种情况如果oldStartVnode和newEndVnode
            // 是同一节点，调用patchVnode进行patch，如果removeOnly是false，那么可以把oldStartVnode.elm移动到oldEndVnode.elm之后，
            // 然后把oldStartVnode设置为下一个节点，newEndVnode设置为上一个节点，重复上述流程
            patchVnode(oldStartVnode, newEndVnode, insertedVnodeQueue, newCh, newEndIdx)
            canMove && nodeOps.insertBefore(parentElm, oldStartVnode.elm, nodeOps.nextSibling(oldEndVnode.elm))
            oldStartVnode = oldCh[++oldStartIdx]
            newEndVnode = newCh[--newEndIdx]

        } else if (sameVnode(oldEndVnode, newStartVnode)) { // Vnode moved left
            //第四种情况如果newStartVnode和oldEndVnode是同一节点，调用patchVnode进行patch，如果removeOnly是false，
            // 那么可以把oldEndVnode.elm移动到oldStartVnode.elm之前，然后把newStartVnode设置为下一个节点
            // oldEndVnode设置为上一个节点，重复上述流程
            patchVnode(oldEndVnode, newStartVnode, insertedVnodeQueue, newCh, newStartIdx)
            canMove && nodeOps.insertBefore(parentElm, oldEndVnode.elm, oldStartVnode.elm)
            oldEndVnode = oldCh[--oldEndIdx]
            newStartVnode = newCh[++newStartIdx]
        } else {

//如果以上都不匹配
            // ，就尝试在oldChildren中寻找跟newStartVnode具有相同key的节点，
            // function createKeyToOldIdx (children, beginIdx, endIdx) {
            //     let i, key
            //     const map = {}
            //     for (i = beginIdx; i <= endIdx; ++i) {
            //         key = children[i].key
            //         if (isDef(key)) map[key] = i
            //     }
            //     return map

            // function findIdxInOld (node, oldCh, start, end) {
            //     for (let i = start; i < end; i++) {
            //         const c = oldCh[i]
            //         if (isDef(c) && sameVnode(node, c)) return i
            //     }
            // }
            if (isUndef(oldKeyToIdx))  //oldKeyToIdx在函数最上方有定义let
                    oldKeyToIdx = createKeyToOldIdx(oldCh, oldStartIdx, oldEndIdx)
                    idxInOld = isDef(newStartVnode.key)
                            ? oldKeyToIdx[newStartVnode.key]
                            : findIdxInOld(newStartVnode, oldCh, oldStartIdx, oldEndIdx)


            if (isUndef(idxInOld)) { // New element
                //如果找不到相同key的节点，
                // 说明newStartVnode是一个新节点，就创建一个，然后把newStartVnode设置为下一个节点
                createElm(newStartVnode, insertedVnodeQueue, parentElm, oldStartVnode.elm, false, newCh, newStartIdx)


            } else {

                //，如果上一步找到了跟newStartVnode相同key的节点，那么通过其他属性的比较来判断这2个节点是否是同一个节点，

                vnodeToMove = oldCh[idxInOld]
                if (sameVnode(vnodeToMove, newStartVnode)) {
                    // // 如果是，就调用patchVnode进行patch，如果removeOnly是false，
                    // 就把newStartVnode.elm插入到oldStartVnode.elm之前，把newStartVnode设置为下一个节点，重复上述
                    patchVnode(vnodeToMove, newStartVnode, insertedVnodeQueue, newCh, newStartIdx)
                    //第五种情况 要删除的
                    oldCh[idxInOld] = undefined //设置为undefined
                    canMove && nodeOps.insertBefore(parentElm, vnodeToMove.elm, oldStartVnode.elm)


                } else {
                   //第六种情况 如果在oldChildren中没有寻找到newStartVnode的同一节点，那就创建一个新节点，把newStartVnode设置为下一个节点，重复上述流程
                    // same key but different element. treat as new element
                    createElm(newStartVnode, insertedVnodeQueue, parentElm, oldStartVnode.elm, false, newCh, newStartIdx)
                }
            }
            //上面的步骤
            newStartVnode = newCh[++newStartIdx]
        }
    }//如果oldStartVnode跟oldEndVnode重合了，并且newStartVnode跟newEndVnode也重合了，这个循环就结束了


     if (oldStartIdx > oldEndIdx) { //如果只有vnode有子节点，那就创建这些子节点
        refElm = isUndef(newCh[newEndIdx + 1]) ? null : newCh[newEndIdx + 1].elm  //用在addVnode上
        addVnodes(parentElm, refElm, newCh, newStartIdx, newEndIdx, insertedVnodeQueue)


    } else if (newStartIdx > newEndIdx) { //newNode先相遇。oldNOde还没相遇，则只有oldVnode有子节点，那就把这些节点都删除
        removeVnodes(parentElm, oldCh, oldStartIdx, oldEndIdx)
    }
}
```


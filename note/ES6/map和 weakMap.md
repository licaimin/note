## ES6新式集合类解析——Map、Set、WeakMap和WeakSet

目前，ES6新引入了四种新的数据结构，它们分别是：映射(Map)、集合(Set)、弱集合(WeakSet)和弱映射(WeakMap)。在本文中，让我们一起学习这四种新增添的集合各自的优势吧。

- 作者：朱先忠编译来源：51CTO|*2016-08-01 16:26*

  [ 收藏](javascript:favorBox('open');)[  分享](javascript:;)

 [![img](https://s5.51cto.com/wyfs02/M02/85/54/wKioL1egNTSTQwLwAACH1-S3HVk979.jpg-wh_651x-s_1140614761.jpg)](https://s5.51cto.com/wyfs02/M02/85/54/wKioL1egNTSTQwLwAACH1-S3HVk979.jpg-wh_651x-s_1140614761.jpg)

**简介**

多数主流编程语言都提供了若干种类型的数据集合支持。例如，Python提供了列表、元组和词典;Java语言中具有列表、集合、映射和队列;Ruby提供了哈希表和数组。然而，JavaScript，直到现在，仅提供了对数组的支持。如你所知，对象和数组一直成为JavaScript编程的主力。目前，ES6新引入了四种新的数据结构，它们分别是：映射(Map)、集合(Set)、弱集合(WeakSet)和弱映射(WeakMap)。在本文中，让我们一起学习这四种新增添的集合各自的优势吧。

**ES5中HashMap的不足分析**

散列、词典和哈希表等是各种编程语言用来存储键/值对这一类数据的数据结构。而且，通常情况下这些数据结构都进行了快速检索方面的优化处理。

在ES5，尽管可以使用JavaScript对象(它们其实就是一些带有键和值的属性的任意集合)来模拟哈希表，但还是存储如下几个不足的地方。

**缺点之1：在ES5中键必须是字符串**

JavaScript对象中的属性键部分必须是字符串类型，这就限制了它们作为不同数据类型的键/值对的集合的能力。当然，你可以强制把其他数据类型转换为字符串，但是这无疑会增加额外的系统负担。

**缺点之2：对象天生就不可迭代**

对象并不是设计作为集合使用的;因此，没有有效的方法来确定一个对象到底有多少属性(例如，Object.keys的效率就很低)。当您遍历某对象的属性时，同时你也获得了该对象的原型属性。诚然，你可以将可迭代的属性添加到所有对象，但并不是所有的对象都为了用作集合而设计的。例如，您可以使用 for …in循环和hasOwnProperty()方法，但这只不是一种变通的解决方法而已。当您遍历对象的属性时，这些属性不必以与插入它们时相同的顺序进行检索。

**缺点之3：与内置方法命名可能发生冲突**

对象都具有内置的方法，如constructor、toString和valueOf。如果其中之一作为一个属性添加到对象上，这很可能会导致冲突。当然，您可以使用Object.create(null)来创建一个空的对象(它不继承自object.prototype);但是，这仍然也只是一种变通的方法而已。

最新的ES6提供了新的集合数据类型;因此，再也不需要使用对象来进行集合模拟并不得不忍受其带来的不足了。

[![img](https://s4.51cto.com/wyfs02/M01/85/48/wKioL1efCizDiL9YAAEtKRxz_Eg796.jpg-wh_600x-s_1756029637.jpg)](https://s4.51cto.com/wyfs02/M01/85/48/wKioL1efCizDiL9YAAEtKRxz_Eg796.jpg-wh_600x-s_1756029637.jpg)

**使用ES6中的MapCollection**

映射是我们要学习的第一个数据结构(或者说“集合”)。映射是任何类型的值/键对的集合。你可以很容易地创建新的映射、添加/删除值、遍历键/值以及有效地确定其大小。下面是这种数据结构提供的几个关键的方法：

**创建映射并使用其常用的方法**

请参考下图中的代码来学习如何创建一个映射和映射中提供的常用方法的用法：

**使用ES6中的SetCollection**

集合是值的有序列表，其中的值是不允许重复的。集合不是像数组一样进行索引，而是通过键来访问的。事实上，集合早已经存在于像Java、Ruby、Python及许多其他语言之中。ES6中的集合和其他语言中的集合的一个重要区别是，ES6中的集合是有顺序的(在许多其他的语言却不是这样)。下图给出集合的几个关键方法的用法举例：

[![img](https://s1.51cto.com/wyfs02/M02/85/48/wKioL1efCgzhp0UtAAF6zY0ObMY884.jpg-wh_600x-s_2313081898.jpg)](https://s1.51cto.com/wyfs02/M02/85/48/wKioL1efCgzhp0UtAAF6zY0ObMY884.jpg-wh_600x-s_2313081898.jpg)

**弱集合、内存与垃圾回收**

JavaScript垃圾回收是一种内存管理技术。在这种技术中，不再被引用的对象会被自动删除，而与其相关的资源也会被一同回收。

Map和Set中对象的引用都是强类型化的，并不会允许垃圾回收。这样一来，如果Map和Set中引用了不再需要的大型对象，如已经从DOM树中删除的DOM元素，那么其回收代价是昂贵的。

为了解决这个问题，ES6还引入了另外两种新的数据结构，即称为WeakMap和WeakSet的弱集合。这些集合之所以是“弱的”，是因为它们允许从内存中清除不再需要的被这些集合所引用的对象。

**使用ES6中的WeakMap**

WeakMap是我们要介绍的第三个新的ES6集合。WeakMap类似于通常的映射(Map)，尽管它提供了更少的方法支持以及存在与前面提到的与垃圾回收有关的不同处理方案。

请参考下图中的代码来了解这种数据结构的创建及其少数的几个方法的使用：

[![img](https://s2.51cto.com/wyfs02/M01/85/48/wKiom1efCeSj5gzjAADO9keOIOY117.jpg-wh_600x-s_1507714720.jpg)](https://s2.51cto.com/wyfs02/M01/85/48/wKiom1efCeSj5gzjAADO9keOIOY117.jpg-wh_600x-s_1507714720.jpg)

**用例分析**

网址<http://stackoverflow.com/questions/29413222/what-are-the-actual-uses-of-es6-weakmap>处提供了几个很受欢迎的有关WeakMap的实例。它们可以用来使一个对象的私有数据“私有”，也可以用来跟踪DOM节点/对象。

**私有数据使用举例**

下面的示例是由JavaScript专家Nicholas C. Zakas先生提供的。

[![img](https://s5.51cto.com/wyfs02/M00/85/48/wKiom1efCaKSR6p9AACDAyNcEz8731.jpg)](https://s5.51cto.com/wyfs02/M00/85/48/wKiom1efCaKSR6p9AACDAyNcEz8731.jpg)

在这里，使用WeakMap简化了保持对象的私有数据真正“私有”的过程。你可以引用Person对象，但在没有特定的Person实例的情况下对privateDataWeakMap的访问是不允许的。

**DOM节点使用举例**

谷歌聚合项目(<https://github.com/Polymer>)中就在一段称为PositionWalker的代码中使用了WeakMaps。其中的PositionWalker方法用于跟踪一棵DOM子树中的位置，这个“位置”对应于当前节点和距离该节点的偏移量。该方法中使用WeakMap来跟踪DOM节点的编辑、删除和变化等。

[![img](https://s3.51cto.com/wyfs02/M02/85/48/wKiom1efCXjQmxAeAADVtZOjBCE961.jpg)](https://s3.51cto.com/wyfs02/M02/85/48/wKiom1efCXjQmxAeAADVtZOjBCE961.jpg)

**使用ES6中的WeakSet**

WeakSet是集合(Set)的集合，当不再需要该集合中的元素引用时可以把它们进行垃圾收集。但是，WeakSet不允许迭代。有关它们的用法相当有限(至少到目前还是很少见)。大多数的早期采用者都说，WeakSet可用于标记对象而不是改变它们。ES6-Features.org网站(<http://es6-features.org/>)上提供了一个从WeakSet中添加和删除元素的例子，目的是为了记录是否对象已被标记过，请参考下图中代码：

[![img](https://s3.51cto.com/wyfs02/M01/85/48/wKioL1efCUXDngaHAAEaIPUDypE870.jpg)](https://s3.51cto.com/wyfs02/M01/85/48/wKioL1efCUXDngaHAAEaIPUDypE870.jpg)

**能否映射一切?**

映射和集合都只是比较新颖的键/值对的集合。也就是说，在许多情况下JavaScript对象仍然可以用作集合。无需切换到的这些新的集合，除非形势需要这样做。

MDN网站(<https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map>)提供了一个问题列表，你可以参考确定何时使用对象或何时使用键/值对的集合：

1.键是否在运行时前一直是未知的?您需要动态查找这些键吗?

2.是否所有的值都具有相同的类型并可以互换使用?

3.你真正需要不是字符串的键吗?

4.你经常添加或删除键-值对吗?

5.你是否有任意(很容易改变)数目的键-值对?

6.你的集合能够迭代吗?

**小结**

以前JavaScript集合被相当有限地使用，但ES6改变了这一点。这些新的集合将为JavaScript语言添加强大功能和灵活性，从而简化JavaScript程序员的开发任务。

# Map

1.  **map**对象是一个简单的键/值映射。任何值（包括对象和原始值）都可以用作一个键或一个值。

```
var m = new Map();
var o = {p: "Hello World"};
m.set(o, "content")
m.get(o) // "content"```

2. **Map**也可以接受一个数组作为参数。该数组的成员是一个个表示键值对的数组。
```

var map = new Map([["name", "张三"], ["title", "Author"]]);
 map.size // 2
 map.get("name") // "张三"
 map.get("title") // "Author"```

1.  **size属性** 返回*Map*结构的成员总数。即返回映射对象中的键/值对的数目。
2.  **set(k,v)** 方法返回的是*Map*本身，因此可以采用*链式*写法。
3.  **get(k)** 方法读取*key*对应的键值，如果找不到*key*，返回*undefined*。
4.  **has(key)** 方法返回一个布尔值，表示某个键是否在*Map*数据结构中。
5.  **delete(key)** 方法删除某个键，返回*true*。如果删除失败，返回*false*。
6.  **clear()** 方法清除所有成员，没有返回值。
7.  **keys()**：返回键名的**遍历器**。
8.  **values()**：返回键值的**遍历器**。
9.  **entries()**：返回所有成员的**遍历器**。
10.  **forEach()**：遍历Map的所有成员。

# WeakMap

1.  **WeakMap**结构与**Map**结构基本类似，唯一的区别是*它只接受对象作为键名（null*除外），不接受其他类型的值作为键名**，而且键名所指向的对象，不计入垃圾回收机制。

```
var map = new WeakMap()
map.set(1, 2)
// TypeError: 1 is not an object!
map.set(Symbol(), 2)
// TypeError: Invalid value used as weak map key
```

1.  *WeakMap*的设计目的在于，**键名是对象的弱引用**（垃圾回收机制不将该引用考虑在内），所以其所对应的对象可能会被自动回收。*当对象被回收后，WeakMap*自动移除对应的键值对**。
2.  *典型应用是，一个对应DOM*元素的*WeakMap*结构，当某个*DOM*元素被清除，其所对应的*WeakMap*记录就会自动被移除。*基本上，WeakMap*的专用场合就是，它的键所对应的对象，可能会在将来消失。*WeakMap*结构有助于防止内存泄漏。
3.  *WeakMap*与*Map*在API上的区别主要是两个，一是**没有遍历操作**（即没有*key()*、*values()*和*entries()*方法），也*没有size*属性**；二是**无法清空*，即不支持clear*方法。这与*WeakMap*的键不被计入引用、被垃圾回收机制忽略有关。
4.  **WeakMap**只有四个方法可用：*get()、set()*、*has()*、*delete()***。

作者：从此以后dapeng

链接：https://www.jianshu.com/p/c0d9d409aa13

来源：简书

简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。
## sort方法
sort()如果要按一定顺序，则传一个函数
sort((a,b)=>{
    return a-b
})
a-b 为升序 1,2,3
b-a 为降序 3,2,1

因为  即 a-b 如果大于0， 则交换位置，如果 a <b,. 则rentu <0 .不改变 所以是从小到大排

```
var arr = new Array(1,5,2,4);
function descCompare(x,y) {
    if(x < y) {
        return 1;
    }
    else if(x === y) {
        return 0;
    }
    else {
        return -1;
    }
}
var newArr = arr.sort(descCompare);
console.log(arr);//会改变原来的数组[5,4,2,1]
console.log(newArr);//[5,4,2,1]
```

### 打乱顺序

```
const shuffle1 = () => {
    const arr = [0, 1, 2, 3, 4];
    for (let i = 1; i < arr.length; i++) {
        const random = Math.floor(Math.random() * (i + 1));
        [arr[i], arr[random]] = [arr[random], arr[i]];
    }
    return arr;
};

const shuffle2 = () => {
    const arr = [0, 1, 2, 3, 4];
    arr.sort(() => Math.random() > .5);
    return arr;
};

```

作者：Lucas HC

链接：https://www.zhihu.com/question/68330851/answer/266506621

来源：知乎

著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

**1) 为什么借助 sort 方法不是真正意义上的完全乱序？**

先证明不完全性。为此实现一个脚本，我对

```js
var letters = ['A','B','C','D','E','F','G','H','I','J'];
```

letters 这样一个数组使用 array.sort  方法进行了 10000 次乱序处理，并把乱序的每一次结果可视化输出。每个元素（ABCD...）出现的位置次数进行记录:

![img](https://pic4.zhimg.com/50/v2-d82ef426dc5445185a27d46976896909_hd.jpg)![img](https://pic4.zhimg.com/80/v2-d82ef426dc5445185a27d46976896909_hd.jpg)

具体脚本实现：[HOUCe/shuffle-array](https://link.zhihu.com/?target=https%3A//github.com/HOUCe/shuffle-array)

不管点击按钮几次，你都会发现整体乱序之后的结果**绝对不是“完全随机”。**

比如 A 元素大概率出现在数组的头部，J 元素大概率出现在数组的尾部，**所有元素大概率停留在自己初始位置。**



**究其原因，**在[Chrome v8引擎源码中](https://link.zhihu.com/?target=https%3A//github.com/v8/v8/blob/master/src/js/array.js)，可以清晰看到，

> v8 在处理 sort 方法时，使用了插入排序和快排两种方案。当目标数组长度小于10时，使用插入排序；反之，使用快排。



其实不管用什么排序方法，大多数排序算法的时间复杂度介于 O(n) 到 O(n2) 之间，元素之间的比较次数通常情况下要远小于 n(n-1)/2，也就意味着有一些元素之间根本就没机会相比较（也就没有了随机交换的可能），这些 sort 随机排序的算法自然也不能真正随机。 

通俗的说，其实我们使用 array.sort 进行乱序，理想的方案或者说纯乱序的方案是：数组中每两个元素都要进行比较，这个比较有 50% 的交换位置概率。如此一来，总共比较次数一定为 n(n-1)。

而在 sort 排序算法中，大多数情况都不会满足这样的条件。因而当然不是完全随机的结果了。
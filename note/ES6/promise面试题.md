这个题目是之前面试的时候遇到的，当时没答对。虽然这种题目看起来对写代码并没什么实际意义，但说到底还是自己对JS执行机制不够深入了解。

就拿这题目拿出来分享给大家一些解题思路。 对JS执行机制不够了解的建议先看了这篇[这一次，彻底弄懂 JavaScript 执行机制 - 掘金](https://juejin.im/post/59e85eebf265da430d571f89)，再食用。

不多说了，上酸菜，哦不对，题目。

```
const first = () => (new Promise((resolve,reject)=>{
    console.log(3);
    let p = new Promise((resolve, reject)=>{
         console.log(7);
        setTimeout(()=>{
           console.log(5);
           resolve(6); 
        },0)
        resolve(1);
    }); 
    resolve(2);
    p.then((arg)=>{
        console.log(arg);
    });

}));

first().then((arg)=>{
    console.log(arg);
});
console.log(4);
复制代码
```

## 第一轮事件循环

先执行**宏任务**，主script ，new Promise立即执行，输出【3】，执行p这个new Promise 操作，输出【7】，发现setTimeout，将回调放入下一轮任务队列（Event Queue），p的then，姑且叫做then1，放入**微任务队列**，发现first的then，叫then2，放入**微任务队列**。执行console.log(4)，输出【4】,宏任务执行结束。

再执行**微任务**，执行then1，输出【1】，执行then2，输出【2】。到此为止，第一轮事件循环结束。开始执行第二轮。

## 第二轮事件循环

先执行**宏任务**里面的，也就是setTimeout的回调，输出【5】。resovle不会生效，因为p这个Promise的状态一旦改变就不会在改变了。 所以最终的输出顺序是3、7、4、1、2、5。

## 总结

对JavaScript执行机制有了解，并且知道Promise构造函数是立即执行的，这个题目相信还是很简单的。

作者：比基尼沙滩第一丧

链接：https://juejin.im/post/5af800fe518825429c594f92

来源：掘金

著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。


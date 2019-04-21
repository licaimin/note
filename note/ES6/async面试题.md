async function async1 () {
  console.log('async1 start')
  await async2();
  console.log('async1 end')
}

async function async2 () {
  console.log('async2')
}

console.log('script start')

setTimeout(function () {
  console.log('setTimeout')
}, 0)

async1();

new Promise (function (resolve) {
  console.log('promise1')
  resolve();
}).then (function () {
  console.log('promise2')
})

new Promise (function (resolve) {
  console.log('promise3')
  resolve();
}).then (function () {
  console.log('promise4')
})

console.log('script end')
运行结果：

script start
async1 start
async2
promise1
promise3
script end
promise2
promise4
async1 end //这个等then方法执行完
setTimeout
首先，要理解宏任务队列和微任务队列，宏任务是代码执行的主线，本来只有一条宏任务线，但如果遇到 setTimeout 之类的就会新建一个宏任务线，然后每个宏任务里面会有一个微任务队列，宏任务在执行过程中如果遇到promise.then()之类的微任务，就会推到当前宏任务的微任务队列当中，在本轮宏任务的同步代码执行完之后，依次执行微任务

首先执行打印script start

然后来到了setTimeout, 新建一条宏任务，排在第一条宏任务后面

执行async1，打印async1 start

执行await async2 , 从右往左执行，执行async2，打印async2, 返回promise对象，

==await会阻塞async1后面的代码执行，所以跳出来==

执行new Promise 打印promise1, 把then 里面的函数加入微任务队列

打印script end

==把async1 await中返回的promise的then 方法加入微队列, 执行await后面的代码需要then 方法的执行，所以这个时候算是宏任务都执行完了，因为卡在那不动了，开始执行微任务==

执行微队列，打印promise2, 执行第二个微任务，啥也没打印

then（）方法参数里拿到结果以后，async2 await算是执行结束了，后面的代码不再被阻塞

打印 await后面的async1 end

来到第二条宏任务线，打印setTimeout

总结一下打印顺序：

script start

async1 start

async2

promise1

script end

promise2

async1 end

setTimeout
--------------------- 
作者：guolinengineer 
来源：CSDN 
原文：https://blog.csdn.net/guolinengineer/article/details/85067924 
版权声明：本文为博主原创文章，转载请附上博文链接！
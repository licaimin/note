## let const var 
（，我们之前所谓的 var 声明提升，是包含 创建和初始化的
这是因为传统的声明变量，例如 var/function 都是会进行 创建和初始化的提升（function特殊一点，赋值也会一并提升）
而ES6：let/const/class 都只是会在 创建 阶段进行提升，而在真正初始化之前，是不能访问变量的，这一区间也就是 Temporal Dead Zone/暂时性死区。）


### let，const共同拥有
1）let是更完美的var，不是全局变量，具有块级函数作用域,大多数情况不会发生变量提升    ==let声明的变量不能通过window.变量名进行访问==
2）其声明的变量有一个块级作用域，    
3）同一个作用域下不能重复声明     
### const
1）const用来声明常量，一旦声明不能更改，所以声明就要初始化      
2）const其实保存的是常量的地址，因为基本类型，栈内存存放的就是它的值
引用类型数据在栈内存中保存的实际上是==对象在堆内存中的引用地址==
所以const可以修改对象属性的值

## 块级作用域
变量不能在语句块之外被访问。避免了全局污染

## 变量提升
JavaScript 中，函数及变量的声明都将被提升到函数的最顶部。
```
function myTestTwo() {
  console.log(a)
    var a = 2;
}
myTestTwo()
输出  undefined
```
myTest();
myTestTwo()
function myTest(){
   console.log('输出')
}
var myTestTwo = function () {
   console.log('输出')
}
# Promise（resolve then()   reject-->catch）
Promise 是异步编程的一种解决方案，有了Promise对象，就可以==将异步操作以同步操作==的流程表达出来，避免了层层嵌套的回调函数。链式调用此外，Promise对象提供统一的接口，使得控制异步操作更加容易。
## 特点
1)一旦状态确定就不会改变，可以随时获得这个结果（跟event有区别）   
2)对象的状态不会受外界影响
## 缺点
1)无法取消Promise，一旦新建它就会立即执行，无法中途取消。     
2)如果不写回调函数，，Promise内部抛出的错误，不会反应到外部。   
3）当处于pending状态，不知道目前进展到哪一状态（刚刚开始还是即将完成）。
```
//异步加载图片

function  asyc(url) {
    return new Promise(function (resolve,reject) {
        const img = new Image();
        img.onload = function () {
            resolve(img)
        };
        img.onerror = function () {
            reject('ERROR')
        };
        img.src = url;
    })
}
```
## race
Promise.race方法同样是将多个 Promise 实例，包装成一个新的 Promise 实例。
上面代码中，只要p1、p2、p3之中有一个实例率先改变状态，p的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给p的回调函数。
js中遍历数组的有两种方式

```
var array=['a']
//标准的for循环
for(var i=1;i<array.length;i++){
    alert(array[i])
}
//foreach循环
for(var i in array){
    alert(array[i])
}
```
正常情况下上面两种遍历数组的方式结果一样。首先说两者的第一个区别

标准的for循环中的i是number类型,表示的是数组的下标,但是foreach循环中的i表示的是数组的key是string类型,因为js中一切皆为对象。自己试试 alert(typeof i);这个区别是小问题。现在我加上如下代码，上面的执行结果就不一样了。

```
//扩展了js原生的Array
Array.prototype.test=function()
 
}
```
试试看上面的代码执行什么。我们发现==标准的for循环任然真正的对数组循环==, 但是此时foreach循环对我刚才写的==test方法写打印出来了==。这就是for与foreach遍历数组的最大区别，如果我们在项目采用的是用foreach遍历数组，假设有一天谁不小心自己为了扩展js原生的Array类，或者引入一个外部的js框架也扩展了原生Array。那问题就来了。再此建议两点

- 不要用for in遍历数组，全部统一采用标准的for循环变量数组(我们无法保证我们引入的js是否会采用prototype扩展原生的Array)
- 如果要对js的原生类扩展的时候，不要采用prototype了
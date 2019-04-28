定义这样一个函数

function doRepeat(func, times, wait) {

}

参数分别是需要 repeat的函数， repeat的次数，每次repeat的间隔

使用方式如下：

调用这个函数能返回一个新函数，比如传入的是alert，这个函数的调用就是
 var repeatedFun = doRepeat(alert, 10, 5000);

调用返回的这个新函数，如: repeatFun("hellworld");

会依次alert十次 helloworld，每次间隔5秒

作者：ZuJung

链接：https://www.jianshu.com/p/976fade63e16

来源：简书

简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。

```
function repeat(func,times,wait) {
     let i = 1;
    return function(){
        const _arg = arguments;
        let timer = setInterval(function () {
          if(i == times) {
              clearInterval(timer)
          }
          func(_arg[0]);
          i++
        },wait)
    }

}
 const repeatFun = repeat(console.log,4,1000);
repeatFun('helloword')
```

使用setimeout

```
function repeat(func,times,wait) {

    let i = 0;

    // 返回函数主体
    function repeat() {
        // 如果次数达到则退出
        if (times === i) {
            return;
        }
        const _args = arguments;

        func(_args[0]);
        i++;

        // 尾递归
        return setTimeout(repeat.bind(this, _args[0]), wait);
    }

    return repeat;

}
 const repeatFun = repeat(console.log,4,1000);
repeatFun('helloword')
```


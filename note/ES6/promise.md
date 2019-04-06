# promise

```
//用promise封装一个ajax
 function ajaxRequest(url) {
     let xhr;
     if(window.XMLHttpRequest){
         xhr = new XMLHttpRequest();
     }else {
         xhr = new ActiveXObject();
     }
     const response = new Promise((resolve, reject) => {
        const  handler = function () {   //这里封装为一个函数
            if(this.readyState !== 4){
                return
            }
            if(this.status === 200){
                resolve(this.response);
            }else {
                reject(new Error(this.statusText));
            }
        };
         xhr.open('GET',url, true);
         xhr.setRequestHeader("Accept","application/json")
         xhr.send();
         xhr.onreadystatechange = handler;
     })

    return response;
 }
 ajaxRequest('../').then(function (json) {
     console.log('resolve',json)
 },function (error) {
     console.log('失败',error)
 })
```

# 实现promise

constructor是一种用于创建和初始化class创建的对象的方法

## 知识补充

### 解构赋值

```js
const {girls, guys, women, men} = state;

// Is the same as

const girls = state.girls;
const guys = state.guys;
const women = state.women;
const men = state.men;

//举个例子
var person = {
    name:'hihi'
}
const {a,b} = person;
person.a = 'a'
console.log(person.a)   输出a
```

```js
// Stage 4(finished) proposal
({a, b, ...rest} = {a: 10, b: 20, c: 30, d: 40});
console.log(a); // 10
console.log(b); // 20
console.log(rest); // {c: 30, d: 40}
```

```
function Promisee(fn){
    const PENDING = Symbol();
    const REJECTED = Symbol();
    const FULFILED = Symbol();
    if(typeof fn !== 'function'){
        throw new Error('not a function')
    }
    let state = PENDING;
    let value = null;
    function fullfill(value) {
        state = FULFILED;
        value = value;
    }
    function reject(err) {
        state = REJECTED;
        value = err;
    }
    //为了防止有错误
    function resolve(result) {
        try {
            fullfill(result)
        }catch (e) {
            reject(e);
        }
    }
    this.then = function(onFullfill,onReject){
        switch (state) {
            case FULFILED:
                onFullfill(value); //搞不懂这一步
                break;
            case REJECTED:
                onReject(value);
                break;
        }
    }
    fn(resolve, reject);
}
let p = new Promisee((resolve ,reject)=>{
    resolve('成功')
})
p.then(
    console.log.bind(null,'error') //搞不懂这一步
)
//输出'成功'
```

```
let p = new Promisee((resolve ,reject)=>{
    setTimeout(()=>{
        resolve('成功')
    },0)
})
//如果是定时器，异步操作的话，发现没有结果

```

这是因为异步操作结束后，我们需要把它放进队列里，然后回调出来？？？

```
let hander = {};
function fullfill(value) {
        state = FULFILED;
        value = value;
        hander.onFullfill(value);

    }
    function reject(err) {
        state = REJECTED;
        value = err;
        hander.onReject(err);
    }
case PENDING:
                hander = {onFullfill,onReject};
                break;
```

```
function Promisee(fn){
   	
    if(typeof fn !== 'function'){
        throw new Error('not a function')
    }
    let state = PENDING;
    let value = null;
    let hander = {};
    function fullfill(value) {
        state = FULFILED;
        value = value;
        console.log(value)
        hander.onFullfill(value);
       

    }
    function reject(err) {
        state = REJECTED;
        value = err;
        hander.onReject(err);
    }
    //为了防止有错误
    function resolve(result) {
        try {
            fullfill(result)
        }catch (e) {
            reject(e);
        }
    }
    this.then = function(onFullfill,onReject){
        console.log(state)
        switch (state) {
            case FULFILED:
                onFullfill(value);
                break;
            case REJECTED:
                onReject(value);
                break;
            //为了支持异步
            case PENDING:
                hander = {onFullfill,onReject};
                break;

        }
    }
    fn(resolve, reject);
}
let p = new Promisee((resolve ,reject)=>{
    setTimeout(()=>{
        resolve('成功')
    },0)
    // resolve('成功')
})
p.then(
    console.log.bind(null,'error')
)
```



上面异步成功后，重新直接调用resolve发现会报错，

因为还没有.then ,就还没调用callback方法

```
let p = new Promisee((resolve ,reject)=>{
    // setTimeout(()=>{
    //     resolve('成功')
    // },0)
    resolve('成功')
})
```



ypeError: hander.onReject is not a function

链式调用

```

function Promisee(fn){
    const PENDING = 'PENDING';
    const REJECTED = 'REJECTED' ;
    const FULFILED = 'FULFILED';
    if(typeof fn !== 'function'){
        throw new Error('not a function')
    }
    this.state = PENDING;
    this.value = null;
    this.queue = [];//存放还未执行的队列
    this.handler = {}
    const _this = this;  //方便this改变时继续调用
    function fullfill(value) {
        _this.state = FULFILED;
        _this.value = value;
        next(_this.handler)
    }
    function reject(err) {
        _this.state = REJECTED;
        _this.value = err
        next(_this.handler)

    }
    //为了防止有错误
    function resolve(result) {
        try {
            fullfill(result)
        }catch (e) {
            reject(e);
        }
    }
    function next({onFullfill,onReject}) {

        switch (_this.state) {
            case FULFILED:
                onFullfill && onFullfill(_this.value); //搞不懂这一步
                break;
            case REJECTED:
                onReject && onReject(_this.value);
                break;
            case PENDING:
                // _this.queue.push({onFullfill,onReject})
                _this.handler = {onFullfill,onReject};
        }
    }
    this.then = (onFullfill,onReject)=>{
        return new Promisee((resolve,reject)=>{
            next({
                onFullfill:val=>{resolve(onFullfill(val))},
                onReject:val=>{reject(onReject(val))}
            })

        })

    }
    fn(resolve, reject);
}
let p1 = new Promisee((resolve ,reject)=>{
    // setTimeout(()=>{
//     resolve('ss')
// },0)
resolve('ss')
})
p1.then((value)=>{
    console.log('then1',value)
    return('end')
}).then((value)=>{
    console.log('end',value)
})
//输出'成功'
```

如果返回的promise对象里有then方法，需要进行检查

```
function sleep(sec) {
    return new Promisee((resolve,reject)=>{
        setTimeout(()=>{
            resolve(sec)
        })
    })
}
let p1 = new Promisee((resolve ,reject)=>{
    // setTimeout(()=>{
//     resolve('ss')
// },0)
resolve('ss')
})
let p2 = p1.then((value)=>{
    console.log('then1',value)
    return sleep(1)
}).then((value)=>{
    console.log('end',value)
    return('再来一个then')
})
p2.then((value)=>{
    console.log(value)
})
```

```
 //为了防止有错误
    function resolve(result) {
        try {
            let then = typeof result.then === 'function'? result.then : null;  //检查返回的promise对象里有没有then方法
            if(then){
                then.bind(result)(resolve,reject)
            }else {
                fullfill(result)

            }
        }catch (e) {
            reject(e);
        }
    }
```

完整版

```javascript
function Promisee(fn){
    const PENDING = 'PENDING';
    const REJECTED = 'REJECTED' ;
    const FULFILED = 'FULFILED';
    if(typeof fn !== 'function'){
        throw new Error('not a function')
    }
    this.state = PENDING;
    this.value = null;
    this.handler = [];//存放还未执行的队列
    const _this = this;  //方便this改变时继续调用
    function fullfill(value) {
        _this.state = FULFILED;
        _this.value = value;
        // next(_this.handler)
        _this.handler.forEach(next);
        _this.handler = null;
    }
    function reject(err) {
        _this.state = REJECTED;
        _this.value = err
        // next(_this.handler)
        _this.handler.forEach(next)
        _this.handler = null;

    }
    //为了防止有错误
    function resolve(result) {
        try {
            let then = typeof result.then === 'function'? result.then : null;  //检查返回的promise对象里有没有then方法
            if(then){
                then.bind(result)(resolve,reject)
            }else {
                fullfill(result)

            }
        }catch (e) {
            reject(e);
        }
    }
    function next({onFullfill,onReject}) {

        switch (_this.state) {
            case FULFILED:
                onFullfill && onFullfill(_this.value); //搞不懂这一步
                break;
            case REJECTED:
                onReject && onReject(_this.value);
                break;
            case PENDING:
                _this.handler.push({onFullfill,onReject})
                // _this.handler = {onFullfill,onReject};
        }
    }
    this.then = (onFullfill,onReject)=>{
        return new Promisee((resolve,reject)=>{
            next({
                onFullfill:val=>{resolve(onFullfill(val))},
                onReject:val=>{reject(onReject(val))}
            })

        })
    }

    fn(resolve, reject);
}
function sleep(sec) {
    return new Promisee((resolve,reject)=>{
        setTimeout(()=>{
            resolve(sec)
        },sec*1000)
    })
}
let p1 = new Promisee((resolve ,reject)=>{
    // setTimeout(()=>{
//     resolve('ss')
// },0)
resolve('ss')
})
let p2 = p1.then((value)=>{
    console.log('then1',value)
    return sleep(3)                 //这个用来停3秒
}).then((value)=>{
    console.log('end',value)
    return('再来一个then')
})
p2.then((value)=>{
    console.log(value)
})
```


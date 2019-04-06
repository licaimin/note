## 箭头函数没有`this`

箭头函数没有`this`，那下面的代码明显可以取到`this`啊：

```
function foo() {
  this.a = 1
  let b = () => console.log(this.a)

  b()
}

foo()  // 1
```



以上箭头函数中的`this`其实是父级作用域中的`this`，即函数`foo`的`this`。箭头函数引用了父级的变量，构成了一个闭包。以上代码等价于：

```
function foo() {
  this.a = 1

  let self = this
  let b = () => console.log(self.a)

  b()
}

foo()  // 1
```



箭头函数不仅没有`this`，常用的`arguments`也没有。如果你能获取到`arguments`，那它一定是来自父作用域的。

```
function foo() {
  return () => console.log(arguments[0])
}

foo(1, 2)(3, 4)  // 1
```



上例中如果箭头函数有`arguments`，就应该输出的是3而不是1。

一个经常犯的错误是使用箭头函数定义对象的方法，如：

```
let a = {
  foo: 1,
  bar: () => console.log(this.foo)
}

a.bar()  //undefined
```



以上代码中，箭头函数中的`this`并不是指向`a`这个对象。对象`a`并不能构成一个作用域，所以再往上到达全局作用域，`this`就指向全局作用域。如果我们使用普通函数的定义方法，输出结果就符合预期，这是因为`a.bar()`函数执行时作用域绑定到了`a`对象。

```
let a = {
  foo: 1,
  bar: function() { console.log(this.foo) }
}

a.bar()  // 1
```



另一个错误是在原型上使用箭头函数，如：

```
function A() {
  this.foo = 1
}

A.prototype.bar = () => console.log(this.foo)

let a = new A()
a.bar()  //undefined
```



同样，箭头函数中的`this`不是指向`A`，而是根据变量查找规则回溯到了全局作用域。同样，使用普通函数就不存在问题。

通过以上说明，我们可以看出，箭头函数除了传入的参数之外，真的是什么都没有！如果你在箭头函数引用了`this`、`arguments`或者参数之外的变量，那它们一定不是箭头函数本身包含的，而是从父级作用域继承的。

## 什么情况下该使用箭头函数

到这里，我们可以发现箭头函数并不是万金油，稍不留神就会踩坑。至于什么情况该使用箭头函数，

1. 箭头函数适合于无复杂逻辑或者无副作用的纯函数场景下，例如用在`map`、`reduce`、`filter`的回调函数定义中；
2. 不要在最外层定义箭头函数，因为在函数内部操作`this`会很容易污染全局作用域。最起码在箭头函数外部包一层普通函数，将`this`控制在可见的范围内；
3. 如开头所述，箭头函数最吸引人的地方是简洁。在有多层函数嵌套的情况下，箭头函数的简洁性并没有很大的提升，反而影响了函数的作用范围的识别度，这种情况不建议使用箭头函数。

## 箭头函数没有this，所以不能用new prototype yield,返回undefined

### 使用 `new` 操作符[节](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Arrow_functions#%E4%BD%BF%E7%94%A8_new_%E6%93%8D%E4%BD%9C%E7%AC%A6)

箭头函数不能用作构造器，和 `new`一起用会抛出错误。

```js
var Foo = () => {};
var foo = new Foo(); // TypeError: Foo is not a constructor
```

### 使用`prototype`属性[节](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Arrow_functions#%E4%BD%BF%E7%94%A8prototype%E5%B1%9E%E6%80%A7)

箭头函数没有`prototype`属性。

```js
var Foo = () => {};
console.log(Foo.prototype); // undefined
```

### 使用 `yield` 关键字[节](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Arrow_functions#%E4%BD%BF%E7%94%A8_yield_%E5%85%B3%E9%94%AE%E5%AD%97)

 `yield` 关键字通常不能在箭头函数中使用（除非是嵌套在允许使用的函数内）。因此，箭头函数不能用作生成器。



# 箭头函数与普通函数的this

严格模式下的普通函数this为undenfied，非严格模式是window；箭头函数的this是定义时所在的this

### 箭头函数this指向注意事项    
箭头函数体内的this对象，如果包裹在函数中就是函数调用时所在的对象，如果放在全局中就是指全局对象window。并且固定不会更改。换句话说内部的this就是外层代码块的this

### 下面是对比分析普通函数和箭头函数中this区别
```
// 普通函数
function foo() {
  setTimeout(function() {
    console.log('id:', this.id);
  });
}

var id = 21;
foo.call({ id: 42 }); //21
//注意定时器，此时this指向window
```

```
// 箭头函数
function foo() {
  setTimeout(() => {
    console.log('id:', this.id);
  }, 100);
}

var id = 21;
foo.call({ id: 42 }); //42
// 上面的匿名函数定义时所在的执行环境就是foo函数，所以匿名
//函数内部的this执向始终会和foo函数的this执向保持一致，不会更改，如同下面的这个案例
function foo() {
  setTimeout(() => {
    console.log('id:', this.id);
  }, 100);
}

var id = 21;
foo(); //21（没有用call）
```

如果不使用 ES6，那么这种方式应该是最简单的不会出错的方式了，我们是==先将调用这个函数的对象保存在变量 _this== 中，然后在函数中都使用这个 _this，这样 _this 就不会改变了。
```
var name = "windowsName";
var a = {
 name : "Cherry",
 func1: function () {
  console.log(this.name)  
 },
 func2: function () {
  var _this = this;
  setTimeout( function() {
   _this.func1()
  },100);
 }
};
a.func2()  // Cherry
```
这个例子中，在 func2 中，首先设置 var _this = this;，这里的 this 是调用 func2 的对象 a，为了防止在 func2 中的 setTimeout 被 window 调用而导致的在 setTimeout 中的 this 为 window。我们将 this(指向变量 a) 赋值给一个变量 _this，这样，在 func2 中我们使用 _this 就是指向对象 a 了。


call的作用就是将foo函数的执行环境从window改成对象{id: 42}

==定时器==的作用就是延迟执行当前函数的外部执行环境，无论有没有设置延迟时间

普通函数解释：定义时this指向函数foo作用域，==但是在定时器100毫秒之后执行函数时，此时this指向window对象==

箭头函数解释：this始终指向定义时所在对象，也就是始终指向foo作用域

进一步分析this
```
var handler = {
  id: '123456',

  init: function() {
    document.addEventListener('click',
      event => this.doSomething(event.type), false);
  },

  doSomething: function(type) {
    console.log('Handling ' + type  + ' for ' + this.id);
  }
};
handler.init()// Handlingclickfor123456
```
箭头函数的this始终指向handler，如果是普通函数，this指向document

this指向的固定化，并不是因为箭头函数内部有绑定this的机制，实际原因是箭头函数根本没有自己的this，导致内部的this就是外层代码块的this。正是因为它没有this，所以也就不能用作构造函数。


### 面试题
下面的面试题一、二、四都设计到引用问题，如果不是很好理解还可以理解成在 es5 中，永远是this 永远指向最后调用它的那个对象。


### 面试题一
```
this.x = 9;    // this refers to global "window" object here in the browser
var module = {
  x: 81,
  getX: function() { return this.x; }
};

module.getX(); // 81

var retrieveX = module.getX;
retrieveX();   
// returns 9 - The function gets invoked at the global scope

// Create a new function with 'this' bound to module
// New programmers might confuse the
// global var x with module's property x
var boundGetX = retrieveX.bind(module);
boundGetX(); // 81
```
retrieveX只是getX函数的引用，也就是只是getX的一个指针（getX的另一个指针是module.getX），所以retrieveX还是指向getX函数本身的

和上面类似的案例，下面的func只是函数引用，所以即使在函数内部，还是执行的函数本身，不受词法作用域限制（箭头函数则受限制）
```
document.getElementById( 'div1' ).onclick = function(){
    console.log( this.id );// 输出: div1
    var func = function(){ 
        console.log ( this.id );// 输出: undefined
    } 
    func();
}; 
//修正后
document.getElementById( 'div1' ).onclick = function(){
    var func = function(){ 
        console.log ( this.id );// 输出: div1
    } 
    func.call(this);
}; 
function foo() {
    console.log( this.a );
}

var a = 2;
var o = { a: 3, foo: foo };
var p = { a: 4 };

o.foo(); // 3
(p.foo = o.foo)(); // 2
```
### 面试题二
```
var A = function( name ){ 
    this.name = name;
};
var B = function(){ 
    A.apply(this,arguments);
};
B.prototype.getName = function(){ 
    return this.name;
};
var b=new B('sven');
console.log( b.getName() ); // 输出:  'sven'
```
### 面试题三
确实，许多包中的函数，和许多在JavaScript语言以及宿主环境中的内建函数，都提供一个可选参数，通常称为“环境（context）”，这种设计作为一种替代方案来确保你的回调函数使用特定的this而不必非得使用bind(..)。

举例来说：
```
function foo(el) {
    console.log( el, this.id );
}

var obj = {
    id: "awesome"
};

// 使用`obj`作为`this`来调用`foo(..)`
[1, 2, 3].forEach( foo, obj ); // 1 awesome  2 awesome  3 awesome
```
### 面试题四
明确绑定 的优先权要高于 隐含绑定
```
function foo() {
    console.log( this.a );
}

var obj1 = {
    a: 2,
    foo: foo
};

var obj2 = {
    a: 3,
    foo: foo
};

obj1.foo(); // 2
obj2.foo(); // 3

obj1.foo.call( obj2 ); // 3
obj2.foo.call( obj1 ); // 2
```
new绑定的优先级高于隐含绑定（new和call/apply不能同时使用，所以new foo.call(obj1)是不允许的，也就是不能直接对比测试 new绑定 和 明确绑定）
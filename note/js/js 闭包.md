# 闭包
闭包是指有权访问另一个函数作用域中的变量的函数
## 网上查的回答
由于在JS中，变量的作用域属于函数作用域，在函数执行后作用域就会被清理、内存也随之回收，但是由于闭包是建立在一个函数内部的子函数，由于其可访问上级作用域的原因，即使上级函数执行完，作用域也不会随之销毁，这时的子函数——也就是闭包，便拥有了访问上级作用域中的变量的权限，即使上级函数执行完后作用域内的值也不会被销毁。
### 创建闭包的方式是
在一个函数内部创建另一个函数
### 闭包的特点
1. 作为一个函数变量的一个引用，当函数返回时，其处于激活状态。       

==2. 一个闭包就是当一个函数返回时，一个没有释放资源的栈区。==   
简单的说，Javascript允许使用内部函数---即函数定义和函数表达式位于另一个函数的函数体内。而且，这些内部函数可以访问它们所在的外部函数中声明的所有局部变量、参数和声明的其他内部函数。当其中一个这样的内部函数在包含它们的外部函数之外被调用时，就会形成闭包

### 闭包之所以能访问
函数执行，会产生执行环境和作用域链，arguments 跟 参数会初始化活动对象，
作用链中，外部函数的活动对象处于第二位    
作用域链实际是 指向活动对象   
而定义在内部的函数会将外部函数的活动对象添加到作用域链中    
即匿名函数被外部函数返回后，它的作用域链被初始化为包含外部函数的活动对象和全局变量对象，外部函数执行完后(其作用域链被销毁)，其活动对象还在，因为内部函数还在引用这个活动对象

### 内存泄漏
```
function neicun(){
    var element = document.getElementById('someElemnt');
    element.onclick = function(){
        alert(element.id)
    }
}   
```
记得解除element的引用  element = null;
```
function neicun(){
    var element = document.getElementById('someElemnt');
    var id = element.id;
    id.onclick = function(){
        alert(id);
    }
    element = null;
}

```
### 模仿块级作用域
```

function a(count){
    (function(){
        for(var i = 0; i<  count; i++){
            console.log('2',i);
        }
    })()
    
    console.log(i);
}
会报错,i is not defined
```
这样的方式常用在全局函数作用域被用在函数外部，从而限制向全局作用域中添加过多的变量和函数
```
(()=>{
    var date = new Date();
    console.log(date.getMonth())
    if(date.getMonth == 1){
        console.log(date.getMonth)
    }
})()
日期到了2月份，就会向用户发出 2月份信息
date是匿名函数的局部变量，不必在全局作用域下创建它
```
## 工厂模式
即封装一个函数，然后进行调用

```
function person(name){
    var o = new Object();
    o.name = name;
    o.sayName = function(){
        console.log(name);
    }
    return o;
}
var person1 = person('hi');
person1.sayName();

```
没有解决对象识别
```
console.log(person1 instanceof person);
输出 false
```
## 构造函数(函数名首字母大写)
new 创建实例 即 person1
Person 为构造函数
```
function Person(name){
    this.name = name;
    this.sayName = function(){
        console.log(name);
    }
}

var person1 = new Person('hi');
person1.sayName();
console.log(person1 instanceof Person); true
console.log(person1 instanceof Object); true 因为继承自Object

console.log(person1.constructor == Person); true
```
与工厂函数的区别
1:没有新建对象
2:没有return 
3：直接将属性和方法赋给了this对象

优点： 可以将它的实例标识为一种特定的类型

缺点： 每个方法都要在实例上创建一遍
```
var person1 = new Person('hi');
var person2 = new Person('hi');
console.log(person1.sayName == person2.sayName)  false
```
解决
因为有this对象
把方法移到外面
```

function Person(name){
    this.name = name;
    this.sayName = sayName
}
function sayName(){
    console.log(this.name)
}
var person1 = new Person('hi');
var person2 = new Person('hi');
console.log(person1.sayName == person2.sayName)  true
```
但是移动到外面即为全局函数
这不是我们想要的，多个方法就多个全局函数，没有封装性可言

# 原型模式
即把方法加在原型上
```
function Person(name){
    this.name = name;
}
Person.prototype.sayName = function()
{
    console.log(this.name);
}
var person1 = new Person('hi');
var person2 = new Person('hi');
console.log(person1.sayName == person1.sayName) true
```
所有的实例共享原型的属性和方法
# 原型对象
创建函数 -> prototype 属性 ->函数的原型对象（自身获得constructor属性）

（函数的prototype.constructor 指向  函数）    
其他方法由Object继承而来

实例 -> [[prototype]指针]   -> 指向函数的原型对象    
没有任何脚本访问这个指针，但Chrome在每个对象都支持一个属性_proto_  

这是 实例 与  构造函数的原型对象  之间的联系   
通过 函数.prototype.isPrototypeOf(实例) 判断之间的联系

SUm
构造函数有一个指向原型对象的指针，原型对象有一个指向构造函数的指针
而实例有一个指向原型对象的指针

#### 所有的实例共享原型的属性和方法`              [的原理 -
==代码读取某个对象的属性==

第一步  实例自身的属性    
第二部  指针指向的原型对象

## 原型链(a函数原型= b函数实例)
a构造函数有一个指向原型对象b的指针，原型对象有一个指向构造函数的指针
而实例c有一个指向原型对象的指针  
另外一个函数A, 原型B 实例C   
如果让原型对b = 实例B   
即原型对象的指针b  指向另一个原型对象B   
这就点成线了   


```
function Person(name, age){
    this.name = name;
    this.age = age;

    this.getInfo = function(){
        console.log(this.name + " is " + this.age + " years old");
    };
}

var will = new Person("Will", 28);
console.log(will.__proto__) 
console.log(Person.prototype.__proto__ == Object.prototype) true

function People() {

}
People.prototype = new Person('hahah');
console.log(People.prototype.__proto__ == Person.prototype) true
// console.log(People.name)

```
Person的实例指向 Person的原型
要让People的原型指向 Person的原型
则让 People的原型 等于 Person的实例
则People的原型指向 Person的原型

[看看这篇文章](https://segmentfault.com/a/1190000008959943)
### 注意
通过原型链继承时，不要用对象字面量创建原型方法
因为这样会重写原型链，即继承Object属性
```
function Person(name){
    this.name = name;
}
Person.prototype = {
    sayName : function(){
        console.log(this.name)
    }
}
var person1= new Person('j');
console.log(person1.sayName())
返回 undefined
```
# 动态原型模式
```
function Person(name){
    this.name = name;
    if(typeof this.sayName !== 'function'){
        Person.prototype.sayName = function(){
            console.log(this.name)
        }
    }
}

```
需要的时候再来添加原型
## 缺点
因为共享，难以传递参数各自使用
## 借用构造函数
```
function Person(name){
    this.name = name;
}
function Newperson(name){
    Person.call(this,name);
}

var person1= new Newperson('A');
var person2= new Newperson('V');
console.log(person1.name) A
console.log(person2.name) V
```
Newperson在调用的时候 ，调用了Person构造函数，
### 缺点
方法都在构造函数中定义，就是构造函数的缺点，即函数不能复用
## 组合继承
```
function Person(name){
    this.name = name;
    
}
function newPerson(name,age){
    Person.call(this,name);
    this.age = age;
}
newPerson.prototype.sayName = function(){
    console.log(this.name);
}


var person1= new newPerson('A',2);
var person2= new newPerson('V',3);
```

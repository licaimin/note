

Vue的本质就是用一个==Function实现的Class==，然后在它的原型prototype和本身上面扩展一些属性和方法。

它的定义是在src/core/instance/index.js里面定义

使用ES5的方式，即用函数来实现一个class，不用ES6来实现class的原因：在ES5中，是可以往Vue的原型上挂很多方法，并且可以将不同的原型方法拆分到不同的文件下，这样方便代码的管理，不用再单个文件上把Vue的原型方法都定义一遍
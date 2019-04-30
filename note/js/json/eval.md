**eval**

eval函数可将一个JavaScript代码字符串求值成特定的对象，所以解析成JSON对象只不过是作用之一。

**为什么eval()解析JSON字符串要加上括号？**

原因是两点：

\1. json对象是以”{}”的方式来开始以及结束的，在JS中，它会被当成一个语句块来处理。

\2. 加上圆括号为了处理字符串为表达式，而不是语句（statement）来执行。

例子：

对象字面量 {}，不加外层的括号，那么eval会识别为JS代码块的开始和结束标记，那么 {} 将会被认为是执行了一句空语句。

```
alert(eval("{}")); // return undefined
alert(eval("({})"));// return object[Object]
```

**不建议使用**

虽然从演示例子看，eval的能力是强过于JSON.parse的，它可解析不规范的JSON字符串，但是G的例子也可以看出，eval是不安全的，特别是数据是第三方给予时候，你根本不知道eval之后它会干什么。

所以结论就是，乖乖用JSON.parse解析JSON对象。

[[JSON.parse与eval的区别](https://www.cnblogs.com/lovesong/p/6036650.html)]

[eval解析JSON字符串的一个小问题](<http://web.jobbole.com/85180/>)

# [JSON.parse vs. eval()](https://stackoverflow.com/questions/1843343/json-parse-vs-eval)

**You are more vulnerable to attacks** if using `eval`: JSON is a subset of Javascript and json.parse just parses JSON whereas `eval` would leave the door open to all JS expressions.


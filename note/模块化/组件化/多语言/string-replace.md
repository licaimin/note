# [js--string/正则表达式replace方法详解](https://segmentfault.com/a/1190000008787668)



- [javascript](https://segmentfault.com/t/javascript/blogs)

 8.2k 次阅读  ·  读完需要 8 分钟

replace方法是javascript涉及到正则表达式中较为复杂的一个方法，严格上说应该是`string对象`的方法。只不过牵扯到正则的时候比较多一些。需要我们灵活的使用。

语法： `stringObj.replace(regexp/substr,replacement)`；

第一个参数：必需。字符串中要替换的子串或正则RexExp；
第二个参数：必需，一个字符串值，规定了替换文本或生成替换文本的函数。 
返回值：注意它的返回值是一个新的字符串，并没有更改原有字符串，是用 replacement 替换了 regexp 的第一次匹配或所有匹配之后得到的。 
所以根据它的参数的不同分为很多种情况，以下一一对各种情况加以分析：

## NO.1 两个参数都是字符串

```
var str1 = '这是一段原始文本，需要替换的内容"这要替换"！';
var newStr = str1.replace('这要替换','need replace');
console.log( newStr );    //输出：   这是一段原始文本，需要替换的内容"need replace"！
```

上面的例子中第二个参数字符串’need replace’,替换掉了第一个参数字符串’这要替换’。这是最简单的一种形式。

## NO.2 第一个参数是正则，第二个参数是字符串

```
var str2 = '这是一段原始文本，需要替换的内容"ac这要替换bb"！';
var newStr = str2.replace( /([a-z])+/g,'qqq' );
console.log( newStr );    //输出：   这是一段原始文本，需要替换的内容"qqq这要替换qqq"！
```

上面的例子字符串’qqq’,替换了正则匹配的内容。如果 regexp 具有全局标志 g，那么 replace() 方法将替换所有匹配的子串。否则，它只替换第一个匹配子串。

## NO.3 第一个参数是正则，第二个参数是带$符的字符串

```
var str3 = '这是一段原始文本,"3c这要替换4d"!';
var newStr = str3.replace( /([0-9])([a-z])/g,"$1" );
console.log( newStr );    //输出：    这是一段原始文本,"3这要替换4"!';
```

![图片描述](https://segmentfault.com/img/bVK2dk?w=568&h=194)

上面的例子，$1表示regexp中的第一个子表示即（[0-9]）匹配单个数字，同理若是$2则表示第二个子表示即（[a-z]）；所以，’3c’这个匹配到的整体被第一个子表示说表示的’3’替换，’4d’被第一个子表示匹配的数字’4’所替换。其他几个同理可得：

（/([0-9])([a-z])/g,”$2″）—>////输出： 这是一段原始文本,”c这要替换d”!'; (3c和4d被相应的第二个子表示匹配出来的c和d替换)
（/([0-9])([a-z])/g,”$'”）—>////输出： 这是一段原始文本,”这要替换d”!这要替换”!”!'; (3c被3c右侧文本替换，4d右侧是”!替换，所以出现俩次)

## NO.4 第一个参数是正则，第二个参数函数

```
var str4 = '这是一段原始文本，需要替换的内容"aa这要bbb替换ccccc"！';
var newStr = str4.replace( /[a-z]+/g,function ($0){
    var str = '';
    for (var i = 0; i < $0.length; i++) {
        str += '*';
    };
    return str;
} );
console.log( newStr );    //这是一段原始文本，需要替换的内容"**这要***替换*****"！
```

上面的例子函数的第一个参数为匹配的regexp的整体，根据长度函数返回值为相应替换的文本；

## NO.5 第一个参数是正则且有子表达式，第二个参数函数且带有多个参数

因为有子表达式	

```
var str5 = '这是一段原始文本，需要替换的内容"3c这要替换4d"！';
var newStr = str5.replace( /([0-9])([a-z])/g,function (arg1,arg2,arg3,arg4,arg5){
 console.log( arg1 );
  console.log( arg2 );
  console.log( arg3 );
  console.log( arg4 );
  console.log( arg5 );
} );
//输出：
3c
3
c
17
这是一段原始文本，需要替换的内容"3c这要替换4d"！
4d
4
d
23
这是一段原始文本，需要替换的内容"3c这要替换4d"！
```

上面的例子第一个参数arg1表示匹配的整体，arg2表示第一个子表达式，arg3表示第二个子表达式，接下来的参数arg4是一个整数，声明了表示子匹配在 stringObject 中出现的位置。最后一个参数是 stringObject 本身。

以上就是replace方法各种可能的情况。确实是一个需要深入理解的方法，不过确实也很强大的一个方法，值得深入研究！
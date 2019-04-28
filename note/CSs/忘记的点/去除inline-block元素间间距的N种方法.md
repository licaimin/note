### 一、现象描述

真正意义上的inline-block水平呈现的元素间，换行显示或空格分隔的情况下会有间距，很简单的个例子：

```
<input /> <input type="submit" />
```

间距就来了~~
![表单控件之间的间距例子](http://image.zhangxinxu.com/image/blog/201204/2012-04-24_162919.png)

我们使用CSS更改非inline-block水平元素为inline-block水平，也会有该问题：

```
.space a {
    display: inline-block;
    padding: .5em 1em;
    background-color: #cad5eb;
}
<div class="space">
    <a href="##">惆怅</a>
    <a href="##">淡定</a>
    <a href="##">热血</a>
</div>
```

![inline-block水平元素间的间距示意 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201204/2012-04-24_163352.png)

您可以狠狠地点击这里：[inline-block元素间间距demo](http://www.zhangxinxu.com/study/201204/inline-block-space-example.html)

这种表现是符合规范的应该有的表现（如果有人认为是bug就太()ay ()oy 了）。

不过，这类间距有时会对我们布局，或是兼容性处理产生影响，需要去掉它，该怎么办呢？以下展示N种方法（欢迎补充）！

### 二、方法之移除空格

元素间留白间距出现的原因就是标签段之间的空格，因此，去掉HTML中的空格，自然间距就木有了。考虑到代码可读性，显然连成一行的写法是不可取的，我们可以：

```
<div class="space">
    <a href="##">
    惆怅</a><a href="##">
    淡定</a><a href="##">
    热血</a>
</div>
```

或者是：

```
<div class="space">
    <a href="##">惆怅</a
    ><a href="##">淡定</a
    ><a href="##">热血</a>
</div>
```

或者是借助HTML注释：

```
<div class="space">
    <a href="##">惆怅</a><!--
    --><a href="##">淡定</a><!--
    --><a href="##">热血</a>
</div>
```

等。

### 四、让闭合标签吃胶囊

如下处理：

```
<div class="space">
    <a href="##">惆怅
    <a href="##">淡定
    <a href="##">热血</a>
</div>
```

注意，为了向下兼容IE6/IE7等喝蒙牛长大的浏览器，最后一个列表的标签的结束（闭合）标签不能丢。

在HTML5中，我们直接：

```
<div class="space">
    <a href="##">惆怅
    <a href="##">淡定
    <a href="##">热血
</div>
```

好吧，虽然感觉上有点怪怪的，但是，这是OK的。

您可以狠狠地点击这里：[无闭合标签去除inline-block元素间距demo](http://www.zhangxinxu.com/study/201204/inline-block-space-skip-close-tag.html)

![无闭合标签与inline-block水平元素间距的去除 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201204/2012-04-24_211852.png)

### 五、使用font-size:0

类似下面的代码：

```
.space {
    font-size: 0;
}
.space a {
    font-size: 12px;
}
```


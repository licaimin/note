发现原理其实很简单：**首先把宽度和高度设置为0，然后设置边框样式，结束**。有点懵逼，没关系，我也是。所以我们还是一步一步来分析下。首先看如下代码：

```
.caret {
  width: 0;
  height: 0;
  border: 50px solid black;
}
```

这个很容易猜到，你会得到一个黑色的正方形。



![img](https:////upload-images.jianshu.io/upload_images/1839822-8dc0b7de7f7701cd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/113/format/webp)



不如把四个边框都换个颜色，看看效果如何：

```
.caret {
  width: 0;
  height: 0;
  border-top: 50px solid black;
  border-right: 50px solid red;
  border-bottom: 50px solid green;
  border-left: 50px solid blue;
}
```

机智的你好像明白了什么......



![img](https:////upload-images.jianshu.io/upload_images/1839822-0e4b10725b97ab92.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/109/format/webp)



我们不妨给 `caret` 设置非零的宽度和高度：

```
.caret {
  width: 50px;
  height: 50px;
  border-top: 50px solid black;
  border-right: 50px solid red;
  border-bottom: 50px solid green;
  border-left: 50px solid blue;
}
```

就会得到这个图案。



![img](https:////upload-images.jianshu.io/upload_images/1839822-e564560b48cfb10d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/157/format/webp)



道理很简单，只是我们平时容易忽略而已。我们小时候学习几何的时候，画的图是很细的直线，包括老师也一直强调，做几何题的时候，计算长宽不用考虑直线的长度（准确的说是厚度）。所以我们脑海中也一直保留着这个错误的观点，但是到了编程领域，完全不同了，你画出来的图是一个像素一个像素拼出来的，即使最细的`border: 1px solid black;`也是占了一个像素。所以，让我们过分一点，把边框宽度设置成50px，你就可以看到计算机是如何处理这种情况的，而且这种处理也是合乎情理的，边框交接处，一边占用一半的面积。

所以，回归到最初的主题，**如何画三角形**。我们改一下第三段代码，左右下边框的==颜色==设置为`transparent`：==不是直接在颜色后面加transparent==

```
.caret {
  width: 0;
  height: 0;
  border-top: 50px solid black;
  border-right: 50px solid transparent;
  border-bottom: 50px solid transparent;
  border-left: 50px solid transparent;
}

/* 如下代码会更优雅点，得到的效果是一样的 */

.caret {
  width: 0;
  height: 0;
  border: 50px solid transparent;
  border-top-color: black;
}
```

所以，你就得到了你想要的三角形了。



![img](https:////upload-images.jianshu.io/upload_images/1839822-96875d66ada73bdb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/105/format/webp)

下三角

结束了吗？还没有，你这样得到的下三角虽然可用，但是本质上占用的空间还是一个正方形，到时候你布局的时候就会发现有点坑，所以我们应该将这个三角形占用的空间尽可能缩小。考虑下第三段代码中，将下边框的长度设置为0，会怎么样？

```
/* 不设置border-bottom即可 */
.caret {
  width: 0;
  height: 0;
  border-top: 50px solid black;
  border-right: 50px solid red;
  border-left: 50px solid blue;
}
```

怎么样，有意思吧。如果你此时把左边框和右边框的颜色设置成 `transparent`，岂不是用最小空间实现了下三角。



![img](https:////upload-images.jianshu.io/upload_images/1839822-b40fbff10fab6927.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/105/format/webp)

**如果只是写了2个方向的border不会有图案(页面不会 有任何东西（width:0  height:0）)，必须要有三个方向的border**
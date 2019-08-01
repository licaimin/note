[原文](https://mp.weixin.qq.com/s/v6R2w26qZkEilXY0mPUBCw?utm_medium=hao.caibaojian.com&utm_source=hao.caibaojian.com)

# 1背景

灯塔是贝壳找房前端架构组推出的一款前端监控系统, 最近和业务方对接时, 被问到了这样一个问题：

> 为什么前端监控要用GIF打点?

这个问题很有意思。我们知道，目前主流的前端监控(百度统计/友盟/谷歌统计)都在用GIF进行打点。但是，为什么这些系统都会使用GIF，难道是因为没有其他的解决方案吗？



这得从前端监控的原理说起。



# 2前端监控的原理

所谓的前端监控，其实是在满足一定条件后，由Web页面将用户信息（UA/鼠标点击位置/页面报错/停留时长/etc）上报给服务器的过程。一般是将上报数据用url_encode（百度统计/CNZZ）或JSON编码（神策/诸葛io）为字符串，通过url参数传递给服务器，然后在服务器端统一处理。



这套流程的关键在于：

1）能够收集到用户信息；

2）能够将收集到的数据上报给服务器。也就是说，只要能上报数据，无论是请求GIF文件还是请求js文件或者是调用页面接口，服务器端其实并不关心具体的上报方式。



向服务器端上报数据，可以通过请求接口，请求普通文件，或者请求图片资源的方式进行。为什么所有系统都统一使用了请求GIF图片的方式上报数据呢？



![img](https://mmbiz.qpic.cn/mmbiz_jpg/Rcon9f6LyEsHY3H8AzKSE1SibR9TM0rNu6Cu3rOicp6LCmjYRp5pQN7rciavUTk1TLmSg65ic0ickjwic9cmZ0jgu7ibw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

可能的方式

##  

# 3为什么主流方案用GIF上报数据

解答这个问题，要用排除法。



首先，为什么不能直接用GET/POST/HEAD请求接口进行上报？



这个比较容易想到原因。一般而言，打点域名都不是当前域名，所以所有的接口请求都会构成跨域。而跨域请求很容易出现由于配置不当被浏览器拦截并报错，这是不能接受的。所以，直接排除。



![img](https://mmbiz.qpic.cn/mmbiz_jpg/Rcon9f6LyEsHY3H8AzKSE1SibR9TM0rNuYUC944uCvuozIWahCbQKq6BEBZ8ucrXerE3icaibRSA5nHuYmT02Xk6A/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

可能的方式_排除接口请求



其次，为什么不能用请求其他的文件资源（js/css/ttf）的方式进行上报？



这和浏览器的特性有关。通常，创建资源节点后只有将对象注入到浏览器DOM树后，浏览器才会实际发送资源请求。反复操作DOM不仅会引发性能问题，而且载入js/css资源还会阻塞页面渲染，影响用户体验。



但是图片请求例外。构造图片打点不仅不用插入DOM，只要在js中new出Image对象就能发起请求，而且还没有阻塞问题，在没有js的浏览器环境中也能通过img标签正常打点，这是其他类型的资源请求所做不到的。



所以，在所有通过请求文件资源进行打点的方案中，使用图片是最好的解决方案。



![img](https://mmbiz.qpic.cn/mmbiz_jpg/Rcon9f6LyEsHY3H8AzKSE1SibR9TM0rNuTkjsKmh8jm0YEUTG6BRdLjFC22ue6PVJFlOOkMTEJU0ghzgz30nlHw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

可能的方式_排除文件请求



那还剩下最后一个问题，同样都是图片，上报时选用了1x1的透明GIF，而不是其他的PNG/JEPG/BMP文件。



这是排除法的最后一步，原因其实不太好想，需要分开来看。



首先，1x1像素是最小的合法图片。而且，因为是通过图片打点，所以图片最好是透明的，这样一来不会影响页面本身展示效果，二者表示图片透明只要使用一个二进制位标记图片是透明色即可，不用存储色彩空间数据，可以节约体积。因为需要透明色，所以可以直接排除JEPG(BMP32格式可以支持透明色)。



然后还剩下BMP、PNG和GIF，但是为什么会选GIF呢？



**因为体积！**



下方是1x1像素透明图，最小的BMP/PNG/GIF文件结构。



**BMP：**

![img](https://mmbiz.qpic.cn/mmbiz_jpg/Rcon9f6LyEsHY3H8AzKSE1SibR9TM0rNu4avZWPvEObNqeL0XEos3mQQ3JyUcmTHgibicsgCic25j1tJ5sV7e5QydA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

BMP

**PNG：**

![img](https://mmbiz.qpic.cn/mmbiz_jpg/Rcon9f6LyEsHY3H8AzKSE1SibR9TM0rNusjwxvOcgTZUcM68MzyRjEeOlJcoZcEEDrSrwMGhzP9sU4HJP6N2JibA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

PNG

**GIF：**

![img](https://mmbiz.qpic.cn/mmbiz_jpg/Rcon9f6LyEsHY3H8AzKSE1SibR9TM0rNuuEreZp2fby9icJe8rcFL3Nu4kJjXjzwSKErUw3IN1jxvTKbLcMXVzsA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

GIF

可以看到，最小的BMP文件需要74个字节，PNG需要67个字节，而合法的GIF，只需要43个字节。



同样的响应，GIF可以比BMP节约41%的流量，比PNG节约35%的流量。这样比较一下，答案就很明显了。



上报数据，显然GIF才是最佳选择。



![img](https://mmbiz.qpic.cn/mmbiz_jpg/Rcon9f6LyEsHY3H8AzKSE1SibR9TM0rNuVMK4WJxaVMPd4lOUDEGhLYibHxMW5eQlMSogqiaLFYlcRVN1ElNBUlGg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

可能的选择_最终结果

##  

# 4总结

前端监控使用GIF进行上报主要是因为：

- 没有跨域问题；
- 不会阻塞页面加载，影响用户体验；
- 在所有图片中体积最小，相较BMP/PNG，可以节约41%/35%的网络资源。



最后，感谢提供支持的各位大佬：

@周晨   提出了这个问题；

@大董   指出加载图片不需要操作DOM，性能更好；

@CC老师 指出在没有JS的环境中，只有图片打点才能正常工作(GET方式需要用户触发)；

@丸九   介绍了各种图片格式的特点，解释了为什么一定要1像素透明图；

@伍子胥 查阅了网上的资料，并确认关键因素：文件体积。

#  

# 5参考资料

**1）StackOverflow上的相关讨论**

(https://stackoverflow.com/a/2083179/4197333)

**2）Smallest possible transparent PNG**

(http://garethrees.org/2007/11/14/pngcrush/)

**3）Github上有人收集了常见文件类型最小体积的demo**

(https://github.com/mathiasbynens/small)

**4）Wikipedia_BMP**

(https://zh.wikipedia.org/wiki/BMP)

**5）PNG格式解析**

(http://dev.gameres.com/Program/Visual/Other/PNGFormat.htm)

**6）GIF图像文件格式**

(http://read.pudn.com/downloads209/doc/984072/GIF%E5%9B%BE%E5%83%8F%E6%A0%BC%E5%BC%8F/GIF%E5%9B%BE%E5%83%8F%E6%96%87%E4%BB%B6%E6%A0%BC%E5%BC%8F.pdf)

**7）最小尺寸的透明图片**

(http://kaifage.com/notes/56/minimum-transparent-image.html)



作   者：大布（企业代号名）

出品人：漠北鹰、旭东（企业代号名）



---------- END ----------
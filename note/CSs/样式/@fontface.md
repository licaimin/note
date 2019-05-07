<https://www.zhangxinxu.com/wordpress/2017/03/css3-font-face-src-local/>

我们使用微软雅黑时，使用引号是如下样式：
[![2016-11-12_192328](https://user-images.githubusercontent.com/1241180/46590438-d6828680-cae5-11e8-84b4-1851b09e06c8.png)](https://user-images.githubusercontent.com/1241180/46590438-d6828680-cae5-11e8-84b4-1851b09e06c8.png)

使用 unicode-range可以解决问题

```
@font-face {
  font-family: BASE;
  src: local('PingFang SC'),
       local("Microsoft Yahei");
}
//这里的意思是：“”使用 SimSun字体
@font-face {
  font-family: quote;
  src: local('SimSun');    
  unicode-range: U+201c, U+201d;
}
.font {
    font-family: quote, BASE;
}
```

U+201c:代表“
<http://unicode.org/cldr/utility/character.jsp?a=201C>
U+201d：代表”
<http://unicode.org/cldr/utility/character.jsp?a=201d>

[![2016-11-12_192511](https://user-images.githubusercontent.com/1241180/46591368-de91f480-caec-11e8-93af-5ef339328aba.png)](https://user-images.githubusercontent.com/1241180/46591368-de91f480-caec-11e8-93af-5ef339328aba.png)

在使用 URL 網址指定字型檔時，除了必要的 `url()` 之外，還可以再加上一個選擇性的 `format()` 來告訴瀏覽器這個字型檔案的格式為何，如果瀏覽器發現它不支援該字型格式，就可以直接跳過這個字型，省去下載字型檔案的時間，如果沒有指定 `format()` 的話，瀏覽器就只能將字型下載之後才能進行判斷是否要使用該字型檔案。

<https://blog.gtwang.org/web-development/css-font-face/>
XSS则：　不相信客户输入的数据
注意:  攻击代码不一定在<script></script>中

将重要的cookie标记为http only,   这样的话Javascript 中的document.cookie语句就不能获取到cookie了.
只允许用户输入我们期望的数据。 例如：　年龄的textbox中，只允许用户输入数字。 而数字之外的字符都过滤掉。
对数据进行Html Encode 处理
过滤或移除特殊的Html标签， 例如: <script>, <iframe> ,  &lt; for <, &gt; for >, &quot for
过滤JavaScript 事件的标签。例如 "onclick=", "onfocus" 等等。
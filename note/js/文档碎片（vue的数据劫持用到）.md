碎片的创建很简单，使用document.createDocumentFragment()即可。

// 创建DocumentFragment碎片
var fragment = document.createDocumentFragment();
 
// 创建HTMLElement对象
var pElement = document.createElement('p');

DocumentFragment接口表示文档的一部分（或一段）。更确切地说，它表示一个或多个邻接的 Document 节点和它们的所有子孙节点
。DocumentFragment 节点不属于文档树，继承的 parentNode 属性总是 null。不过它有一种特殊的行为，该行为使得它非常有用，即当请求把一个 DocumentFragment 节点插入文档树时，插入的不是DocumentFragment 自身，而是它的所有子孙节点。这使得 DocumentFragment 成了有用的==占位符==，暂时存放那些一次插入文档的节点。它还有利==于实现文档的剪切、复制和粘贴操作==，尤其是与 Range 接口一起使用时更是如此。


比如下面这段代码，我们先创建div元素插入到碎片中，最后将碎片插入到container中。这样，碎片下的所有子孙节点都会被插入到container下。
```
var fragment = document.createDocumentFragment();
for (var i=0; i<100; i++) {
	var div =document.createElement('div');//创建一个dom节点
	div.innerHTML = 'aaa' + i;//给节点添加内容
	fragment.appendChild(div);//把创建的节点插入到碎片中
}
 
$("#container").append(fragment);
```
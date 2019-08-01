## 1. 删除表格的某一行

1、var row=table.insertRow();添加一行；

2、var cell1=row.insertCell();添加一个单元格；(如果在后面继续写var cell2=row.insertCell()的话，就是添加第二列;)

3、cell1.innerHTML="第一列的内容"；向第一个单元格中填充值

4、删除行的方法：deleteRow();添加行则是insertRow();

5、删除列,即是删除单元格，方法为：deleteCell()；而添加列则是insertCell()

以上就是昨天记录的动态添加一行的全部了，当然能够添加就能删除，而今天来记录的就是动态的删除，删除一行，删除一列。首先看删除一行：

我们先来看存在的表格：

这样，现在存在一个四行两列的表格，我们先来实现删除某指定一行：假定我们需要删除第三行，我们该怎么写呢？

这样来看一下代码：在html代码中，在delRow按钮上添加方法οnclick="c()";

function c(){
	var table=document.getElementById("tad");
	var len=table.rows.length;
	table.deleteRow(len-2);//这里删除的是倒数第二行，也就是第三行
}
这样我们来运行下，结果显示为：


这样，第三行就被删除了，由此我们可以得知，删除一行的方法为deleteRow(index)，index为参数，表示第几行，这个参数时从上向下，由0开始数的，另外有特别需要注意的一点：如果参数不写，则效果与参数为0一样，表示删除最上面一行

这样实现删除所有行是不是就有思路了，这样我们来写下代码：

function c(){
	var table=document.getElementById("tad");
	var len=table.rows.length;
	for(var i=0;i<len;i++){
	    table.deleteRow();//也可以写成table.deleteRow(0);
	}
}
这样我们来看下结果：


就==只剩下table的外壳了==，里面的内容全都不见了，原理我们懂了，代码我们也实现了，但是在实现过程中有几点我们需要注意：

1、在循环中我们是首先获取的固定值，var len=table.rows.length;然后i<len，而不是直接写i<table.rows.length;

想必大家都明白其中的原因，删除一行之后，在进入第二次循环的时候，表格已经变动了，则table.rows.length也改变了，然而i也增大了，等到table.row.length<=i的时候行并没有全部删光，在这个例子中的话应该是i=2的时候table.rows.length也等于2了，则就不再进行删除了，所以会余下两行，解决的办法之一，当然就是按我写这样，另一种也可以把i++去掉，知道len=0的时候停止也可以，但是理解起来有点麻烦了就

2、在循环中我们写的是table.deleteRow()或者==table.deleteRow(0)==,而不是table.deleteRow(i)，跟1中的原因一样的哦





接下来我们再来记录下删除列，如果说行是deleteRow()的话，列该怎么写呢，这里没有cols的事情，事实上就是之前添加的单元格啊，将每一行的同一列上的单元格全部删除掉不就等同于删除了一列么，删除单元格的方法同样跟添加是对应的==deleteCell()==;

这样如果说只删除固定列，怎么写也就呼之欲出了吧，继续就上面的表格进行操作，删除第三行第二列，我们来写下实现代码：

function d(){
	var table=document.getElementById("tad");
	table.rows[2].deleteCell(1);
}

这个结果太明显了吧，那样所有列都删除也就容易多了，来继续实现下代码：

function d(){
	var table=document.getElementById("tad");
	for(var i=0;i<table.rows.length;i++){
		table.rows[i].deleteCell(1);
	}
}

这个结果也就随之而来了，这样我们就实现了动态的删除行和列，我们再来总结下：

```
<!DOCTYPE html>
<html>
<head>
<script>
function insRow(id) {
  var x = document.getElementById(id).insertRow(0);
  var y = x.insertCell(0);
  var z = x.insertCell(1);
  y.innerHTML = z.innerHTML = "New";
}
</script>
</head>

<body>
<table id="myTable" style="border: 1px solid black">
  <tr>
    <td>Row1 cell1</td>
    <td>Row1 cell2</td>
  </tr>
  <tr>
    <td>Row2 cell1</td>
    <td>Row2 cell2</td>
  </tr>
  <tr>
    <td>Row3 cell1</td>
    <td>Row3 cell2</td>
  </tr>
</table>

<p>
<input type="button" onclick="insRow('myTable')" value="插入行">
</p>

</body>
</html>

```


- `forEach()`: 针对每一个元素执行提供的函数(executes a provided function once for each array element)。
- `map()`: ==创建一个新的数组==，其中每一个元素由调用数组中的每一个元素执行提供的函数得来

到底有什么区别呢？`forEach()`方法不会返回执行结果，而是`undefined`。也就是说，`forEach()`会修改原来的数组。而`map()`方法会得到一个新的数组并返回。

```
let arr = [1,2,3,4]
arr.forEach((item,index)=>{
   return arr[index] = item *2  
})
console.log(arr)
let newArray = arr.map((item)=>{
    return item*2
})

console.log(newArray)
```

## 哪个更好呢？

取决于你想要做什么。

`forEach`适合于你并不打算改变数据的时候，而只是想用数据做一些事情 – 比如存入数据库或则打印出来。

```
let arr = ['a', 'b', 'c', 'd'];
arr.forEach((letter) => {
    console.log(letter);
});
// a
// b
// c
// d
```

`map()`适用于你要改变数据值的时候。不仅仅在于它更快，而且返回一个新的数组。这样的优点在于你可以使用复合(composition)(map(), filter(), reduce()等组合使用)来玩出更多的花样。

```
let arr = [1, 2, 3, 4, 5];
let arr2 = arr.map(num => num * 2).filter(num => num > 5);
// arr2 = [6, 8, 10]
```

我们首先使用map将每一个元素乘以2，然后紧接着筛选出那些大于5的元素。最终结果赋值给`arr2`。

## 核心要点

- 能用`forEach()`做到的，`map()`同样可以。反过来也是如此。
- `map()`会分配内存空间存储新数组并返回，`forEach()`不会返回数据。
- `forEach()`允许`callback`更改原始数组的元素。`map()`返回新的数组。


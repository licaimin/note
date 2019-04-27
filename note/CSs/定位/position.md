- position

  属性指定用于元素的定位方法的类型（静态，相对，固定，绝对或粘性）。 有五种不同的值：

  - static
  - relative
  - fixed
  - absolute
  - sticky

  然后使用

  top

  ，

  bottom

  ，

  left

  和

  right

  属性定位元素。但是，除非首先设置

  position

  属性，否则这些属性将不起作用。根据位置值，它们的工作方式也不同。

- 

  # position:static;

  默认情况下，HTML元素定位为静态。静态定位元素不受top，bottom，left和right属性的影响。 元素

  position:static;

  没有以任何特殊方式定位; 它总是根据页面的正常流程定位：

  这个<div>元素的position:static;

  ```
  源代码div.static {        position: static;        border: 3px solid #73AD21;      }
  ```

- 

  # position:relative;

  具有

  position:relative;

  相对于==其(本身)正常位置定位==的元素。设置相对定位元素的

  top

  ，

  bottom

  ，

  left

  和

  right

  属性将使其远离其正常位置进行调整。其他内容不会被调整以适应元素留下的任何空白。

  这个<div>元素的position:relative;

  ```
  源代码div.relative {        position: relative;        left: 30px;        border: 3px solid #73AD21;      }
  ```

- 

  # position:fixed;

  元素

  position:fixed;

  相对于==视口(浏览器)定位==，这意味着即使页面滚动，它也始终保持在同一位置。

  top

  ，

  bottom

  ，

  left

  和

  right

  属性用于定位元素。固定元素不会在页面中留下通常位于其中的间隙。注意页面右下角的固定元素。

  ```
  源代码div.fixed {        position: fixed;        bottom: 0;        right: 0;        width: 300px;        border: 3px solid #73AD21;      }
  ```

- 

  # position:absolute;

  具有

  position:absolute;

  相对于==最近定位的祖先定位的元素==（而不是相对于视口定位，如fixed）。然而; 如果绝对定位元素没有定位祖先，它将==使用文档正文（不是 body 也是html 而是页面文档）==。

   

  注意

  ： “定位”元素的位置是除了

  static

  之外的任何元素。

  ```
  源代码div.relative {        position: relative;        width: 400px;        height: 200px;        border: 3px solid #73AD21;      }       div.absolute {        position: absolute;        top: 80px;        right: 0;        width: 200px;        height: 100px;        border: 3px solid #73AD21;      }
  ```

- 

  # position:sticky;

  position:sticky;

  根据==用户的滚动位置定位元素==。粘性元素在==relative和之间切换fixed==，在目标区域以内，它的行为就像 position:relative; 而当页面滚动超出目标区域时，它的表现就像 position:fixed;，它会固定在目标位置。	

  ==在指定的父级窗口里才有效==

  > **注意**： Internet Explorer，Edge 15及更早版本不支持粘性定位。Safari需要-webkit-前缀（参见下面的示例）。您还必须指定的至少一个top，right，bottom或left为粘稠的定位工作。

  在此示例中，top: 0当您到达其滚动位置时，粘性元素会粘到页面顶部。

  ```
  源代码div.sticky {        position: -webkit-sticky;        position: sticky;        top: 0;        padding: 5px;        background-color: #cae8ca;        border: 2px solid #4CAF50;      }
  ```

俗话说，弄不清楚原理，就熟记结论也是一样的，那就抛出结论，sticky满足以下条件才能生效：

1、具有sticky属性的元素，其==父级高度必须大于sticky元素的高度==。

2、sticky元素的底部，不能和父级底部重叠。（这条不好表述，文后详细说明）

3、sticky元素的父级不能含有overflow:hidden 和 overflow:auto 属性

4、 本身不能设置为inline,不然会失效

同时要注意，sticky元素仅在他父级容器内有效，超出容器范围则不再生效了。
--------------------- 

## 目录

[场景导入](http://vip.yypm.com/sharebook/20170612-CSS%E9%87%8D%E6%9E%84%E9%82%A3%E4%BA%9B%E4%BA%8B.html#1)

[css规范-acss](http://vip.yypm.com/sharebook/20170612-CSS%E9%87%8D%E6%9E%84%E9%82%A3%E4%BA%9B%E4%BA%8B.html#2)

[css规范-oocss](http://vip.yypm.com/sharebook/20170612-CSS%E9%87%8D%E6%9E%84%E9%82%A3%E4%BA%9B%E4%BA%8B.html#3)

[css规范-smacss](http://vip.yypm.com/sharebook/20170612-CSS%E9%87%8D%E6%9E%84%E9%82%A3%E4%BA%9B%E4%BA%8B.html#4)

[css规范-bem](http://vip.yypm.com/sharebook/20170612-CSS%E9%87%8D%E6%9E%84%E9%82%A3%E4%BA%9B%E4%BA%8B.html#5)

[css工作流](http://vip.yypm.com/sharebook/20170612-CSS%E9%87%8D%E6%9E%84%E9%82%A3%E4%BA%9B%E4%BA%8B.html#6)

[css模块化](http://vip.yypm.com/sharebook/20170612-CSS%E9%87%8D%E6%9E%84%E9%82%A3%E4%BA%9B%E4%BA%8B.html#7)

[总结](http://vip.yypm.com/sharebook/20170612-CSS%E9%87%8D%E6%9E%84%E9%82%A3%E4%BA%9B%E4%BA%8B.html#8)

[Top](http://vip.yypm.com/sharebook/20170612-CSS%E9%87%8D%E6%9E%84%E9%82%A3%E4%BA%9B%E4%BA%8B.html#top)

## 场景导入

场景1：命名难。

不知道大家有没有试过为了让类名不一样，居然用中文拼音+英文来命名的傻事

场景2：维护困难。

当维护别人的代码时，看到选择器是一连串的结构逻辑，看不过来，嫌麻烦，然后为了覆盖它的样式，起了个类名重新写样式， 但是之前的权限比较重，直接给样式设置了 **!important**。（ps: ！important忘不得已不用，对以后维护产生麻烦） 若每次改动都增加一套样式，那么一个元素会对应多套样式，遍布整个样式文件...

场景3：性能差

```css
.items-box{
    li {
        height: 35px;
        line-height: 35px;
        position: relative;
        z-index: 1;
    }
    .first-li {
        li {
            height: 33px;
            line-height: 33px;
            font-weight: 600;
        }
        .inner-ul li {
            border-bottom: 2px solid #eeeeee; 
        }
    }
}
```

以上代码我们在工作中常写，经过编译生成的css是这样的 `.items-box .first-li .inner-ul li { border-bottom: 2px solid #eeeeee; }`

可以看到嵌套了多层选择器，性能很差的。因为css选择器样式系统从最右边的选择符开始向左进行匹配，只要当前选择符的左边还有其他选择符，样式系统就会继续向左移动，直到找到和规则匹配的元素，或者因为不匹配而退出。

从以上场景可以总结，写css有三个痛点，命名难，维护性差，性能差。编写一些高质量的css代码，让css具有扩展性、复用性、可维护性是我们写好css代码的方向。

## css规范-acss

是一系列常用的基本类，包括文字、定位、长宽和边距。 有两个特性，通用+原子 通用: 是网站最常用的类，任何页面都可以随意使用它们 原子: 是最基础的样式，一个类只设置一个样式，不可再分 一般命名尽量简短，方便记忆和调用。fontSize12 ->f12

下面是摘自京东的代码

```
*文字排版*/
.f12{font-size:12px} 
.f13{font-size:13px} 
.f14{font-size:14px} 
.f16{font-size:16px} 
.f20{font-size:20px} 
.fb{font-weight:bold} 
.fn{font-weight:normal} 
.t2{text-indent:2em} 
.lh150{line-height:150%} 
.lh180{line-height:180%} 
.lh200{line-height:200%} 
.unl{text-decoration:underline;} 
.no_unl{text-decoration:none;}
/*定位*/
.tl{text-align:left}
.tc{text-align:center}
.tr{text-align:right}
.bc{margin-left:auto;margin-right:auto;}
.fl{float:left;display:inline}
.fr{float:right;display:inline}
.cb{clear:both}
.cl{clear:left}
.cr{clear:right} 
.clearfix:after{content:".";display:block;height:0;clear:both;visibility:hidden}
.clearfix{display:inline-block}* html .clearfix{height:1%}.clearfix{display:block}
.vm{vertical-align:middle}
.pr{position:relative}
.pa{position:absolute}
.abs-right{position:absolute;right:0}
.zoom{zoom:1}
.hidden{visibility:hidden}
.none{display:none}
/*长度高度*/ 
.w10{width:10px} 
.w20{width:20px} 
.w30{width:30px} 
.w40{width:40px} 
.w50{width:50px} 
.w60{width:60px} 
.w70{width:70px}
.w80{width:80px}
.w90{width:90px}
.w100{width:100px}
.w200{width:200px}
.w250{width:250px}
.w300{width:300px}
.w400{width:400px}
.w500{width:500px}
.w600{width:600px}
.w700{width:700px}
.w800{width:800px}
.w{width:100%}
.h50{height:50px}
.h80{height:80px}
.h100{height:100px}
.h200{height:200px}
.h{height:100%}
/*边距*/ 
.m10{margin:10px} 
.m15{margin:15px} 
.m30{margin:30px} 
.mt5{margin-top:5px} 
.mt10{margin-top:10px} 
.mt15{margin-top:15px} 
.mt20{margin-top:20px} 
.mt30{margin-top:30px} 
.mt50{margin-top:50px} 
.mt100{margin-top:100px}
.mb10{margin-bottom:10px}
.mb15{margin-bottom:15px}
.mb20{margin-bottom:20px}
.mb30{margin-bottom:30px}
.mb50{margin-bottom:50px}
.mb100{margin-bottom:100px}
.ml5{margin-left:5px}
.ml10{margin-left:10px}
.ml15{margin-left:15px}
.ml20{margin-left:20px}
.ml30{margin-left:30px}
.ml50{margin-left:50px}
.ml100{margin-left:100px}
.mr5{margin-right:5px}
.mr10{margin-right:10px}
.mr15{margin-right:15px}
.mr20{margin-right:20px}
.mr30{margin-right:30px}
.mr50{margin-right:50px}
.mr100{margin-right:100px}
.p10{padding:10px;}
.p15{padding:15px;}
.p30{padding:30px;}
.pt5{padding-top:5px}
.pt10{padding-top:10px}
.pt15{padding-top:15px}
.pt20{padding-top:20px}
.pt30{padding-top:30px}
.pt50{padding-top:50px}
.pb5{padding-bottom:5px}
.pb10{padding-bottom:10px}
.pb15{padding-bottom:15px}
.pb20{padding-bottom:20px}
.pb30{padding-bottom:30px}
.pb50{padding-bottom:50px}
.pb100{padding-bottom:100px}
.pb150{padding-bottom:150px}
.pl5{padding-left:5px}
.pl10{padding-left:10px}
.pl15{padding-left:15px}
.pl20{padding-left:20px}
.pl30{padding-left:30px}
.pl50{padding-left:50px}
.pl100{padding-left:100px}
.pr5{padding-right:5px}
.pr10{padding-right:10px}
.pr15{padding-right:15px}
.pr20{padding-right:20px}
.pr30{padding-right:30px}
.pr50{padding-right:50px}
.pr100{padding-right:100px}
```

优点： 1.便于抽出复用代码，提高代码复用程度。清除浮动clearfix就是最好的一个例子，同时原子类的使用能够使得我们编写样式的时候更加专注于业务逻辑的样式。 2.对于简单的样式，原子类可以直接通过堆叠来完成，避免了不必要的命名，减少命名与记忆成本。 3.CSS 框架的应用。很多前端框架的 CSS 部分，比如是网格系统、栅格系统，都是原子类的具体应用

```
/* bootstrap栅格系统 */
.col-xs-1, 
.col-xs-2, 
.col-xs-3, 
.col-xs-4, 
.col-xs-5, 
.col-xs-6, 
.col-xs-7, 
.col-xs-8, 
.col-xs-9, 
.col-xs-10, 
.col-xs-11, 
.col-xs-12 { float: left; } 
.col-xs-12 { width: 100%; } 
.col-xs-11 { width: 91.66666667%; } 
.col-xs-10 { width: 83.33333333%; } 
.col-xs-9 { width: 75%; } 
.col-xs-8 { width: 66.66666667%; } 
.col-xs-7 { width: 58.33333333%; } 
.col-xs-6 { width: 50%; } 
.col-xs-5 { width: 41.66666667%; } 
.col-xs-4 { width: 33.33333333%; } 
.col-xs-3 { width: 25%; } 
.col-xs-2 { width: 16.66666667%; } 
.col-xs-1 { width: 8.33333333%; }
```

缺点： 对维护造成了一定的困难。用了m20的原子类，会遍布网页中的很多div，突然改需求了，magin变为15, 那估计会爆炸💥。

即便如此，依然无法掩盖原子类的好处，举个例子，以下代码完全通过原子类的堆叠生成最终的样式，减少了命名（程序员五大难题之一），而且编程体验也很好。

```javascript
<div class="h150 w1198 auto mt20 mb30 border" :controller="info">
    <img class="fl bgred h70 w70 mt40 mr20 ml30" />
    <div class="w200 mr150 mt40 fl">
        <div class="cmain f12 lh100 mb16 f">昵称：<span class="cred f">我是大赢家</span></div>
        <div class="cmain f12 lh100 mb16 f">YY号：123456789</div>
        <div class="f12 lh100 cmain f">账号余额：<span class="cred f">12幸运币</span></div>
    </div>
    <div class="fl f12 lh100 mt40">
        <div class="cmain fl f">填写手机号码，以便接收中奖信息</div>
        <div class="cblue ml15 fl pointer" :click="wAddr">填写</div>
    </div>
</div>
```

应用场景：

1. 一些样式比较简单的页面，直接组合原子类，就能够完成页面的设计，省去想类名的麻烦。 2.配合bem，可以做模块的扩展性作用，不同的地方用原子类实现。

## css规范-oocss

OOCSS是（Object Oriented CSS），顾名思义就是面向对象的CSS。

OOCSS主要有两个原则：

#### 1、结构和样式分离

我们平时一定遇到过这种情况，比如一个页面存在着多个不同功能的按钮，这些按钮的形状大小都差不多，但是根据不同的功能会有不同的颜色或背景来加以区分。如果不进行结构和样式分离，我们的CSS代码可能是这样的

```javascript
    .btn-primary {
        width: : 100px;
        height: 50px;
        padding: 5px 3px;
        background: #ccc;
        color: #000;
    }
    .btn-delete {
        width:100px;
        height:50px;
        padding:5px 3px;
        background:red;
        color:#fff;
    }
```

这两个或者可能更多的按钮拥有一些不同的样式，但是它们同时拥有相同的大小样式等，我们将其抽象的部分提取出来，结果如下：

```javascript
    /**结构和样式分离**/
    .btn{
        width:100px;
        height:50px;
        padding:5px 3px;
    }
    .primary{
        background:red;
        color:#fff;
    }
    .delete{
        background:red;
        color:#fff;
    }
```

这样提取公用的样式出来，然后按钮同时引用btn和primary等。这种做法除了减少重复的代码精简CSS之外，还有一个好处是复用性，如果需要增加其他额外的按钮，只需要编写不同的样式，和btn配合使用即可。 再举个例子 这部分在网站中多次出现，并且作用于不同的元素上。看看下面的代码，它可以作用到一个盒子，一个图片，或者一个按钮上

```javascript
    #my-button,
    .my-box,
    .my-box img {
      border: 1px solid #444;
      border-radius: 5px;
      box-shadow: 0 0 5px rgba(0,0,0,0.1);
    }
```

另外我们可以用一个叫做.skin的类名代替，然后应用到所要使用的元素上

```javascript
    .skin {
      border: 1px solid #444;
      border-radius: 5px;
      box-shadow: 0 0 5px rgba(0,0,0,0.1);
    }
```

#### （2）容器和内容分离

可以总结下， OOCSS风格的css可以描述为两点： 1.增加class 2.不使用继承选择符 OOCSS追求元件的复用，其class命名比较抽象，一般不体现具体内容。

## css规范-SMACSS

三条原则： 1. 为css分类 2.命名规则: 3.css有五个类别 **基础样式**: 是任何场合下，页面元素的默认外观, 它的定义不会用到class和ID.**css reset**也属于此类 **布局样式**: 元素是有层次级别之分的,通常比较大块的结构就属于布局布,用于实现网页的基本布局，搭起整个网页的基本骨架。; 左右分栏、栅格系统等都属于布局样式; 使用**.l-head,.l-body,.l-sideba**r等 **模块样式**: 模块放置布局样式之下，是独立的, 可以在各种场合使用; **状态样式**: 表示动态的、具有交互性质的状态样式; 使用**.is-on,.is-active**等以.is开头的前缀 **主题样式**: 描述了页面主题外观，一般是指颜色、背景图。一般是用在tab切换，背景颜色的变化等(相当于换肤) 使用**.cup-skin,.people-skin**等以**-skin**结尾的样式。 3.最小化适度深配 和OOCSS的分离容器和内容规则一样，避免使用后代选择器，而是直接取个类名。只要为元素添加class，就可以获得对应样式。 SMACSS着力于实现两个主要目标： 1.更语义化的html和css 2.降低对特定html结构的依赖

## 常用CSS class名

**包裹类：** container, wrapper, outer, inner, box, header, footer, main, content, aside, page, section, block

**状态类：** primary, secondary, success, danger, warning, info, error, Link, light, dark, disabled, active, checked, loading

**尺寸类：** large, middle, small, bigger, smaller

**组件类：** card, list, picture, carousel, swiper, menu, navs, badge, hint, modal, dialog

**位置类：** first, last, current, prev, next, forward, back

**文本类：** title, desc, content, date, author, category，label，tag

**人物类：** avatar, name, age, post, intro

## css规范-BEM

BEM是Block，Element，Modifier的缩写。下面分别来介绍一下这三个概念：

（1）Block：在BEM的理论中，一个网页是由block组成的，比如头部是个block，内容是block，logo也是block，一个block可能由几个子block组成。

（2）Element：element是block的一部分，具有某种功能，element依赖于block，比如在logo中，img是logo的一个element，在菜单中，菜单项是菜单的一个element

（3）Modifier：modifier是用来修饰block或者element的，它表示block或者element在外观或行为上的改变

我们通过BEM命名法写样式如下：

```javascript
    .block{}
    .block__element{}
    .block--modifier{}
    .block__element--modifier{}
```

BEM将页面解析为block和element，然后根据不同的状态使用modifier来设置样式。

举个例子：

```javascript
  <div class="lucky__wrap">
        <ul class="lucky__box">
            <li class="lucky__item">1</li>
            <li class="lucky__item">2</li>
            <li class="lucky__item">3</li>
            <li class="lucky__item">4</li>
        </ul>
        <ul class="lucky__dotWrap">
            <li class="lucky__dot"></li>
            <li class="lucky__dot"></li>
            <li class="lucky__dot"></li>
            <li class="lucky__dot lucky__dotLast" ></li>
        </ul>
        <div class="lucky__arrow lucky__arrow--left"></div>
        <div class="lucky__arrow lucky__arrow--right" ></div>
    </div>
```

优点：BEM 命名给 CSS 以及 html 提供清晰结构，命名空间提供更多信息，模块化提高代码的重用，以达到 CSS 命名语义化、可重用性高、后期维护容易、加载渲染快的要求。

缺点：BEM 命名会使得 Class 类名变长 解决办法：用stylus的语法，&可以和之前一样嵌套写样式，最终编译的时候是一个类名

```javascript
.lucky {
    &__wrap {
        width: 254px;
        height: 386px;
        overflow: hidden;
        position: relative;
        &:hover {
            top: -4px;
            box-shadow:0 8px 10px 3px #e1e1e1;
        }
    }
    &__box {
        width: 1016px;
        position: relative;
        left: 0;
    }
    &__item {
        float: left;
        width: 254px;
        height: 386px;
    }
    &__dotWrap {
        position: absolute;
        left: 88px;
        bottom: 12px;
    }
    &__dot {
        display: inline-block;
        height: 3px;
        width: 16px;
        background: #d7c6b3;
        margin-right: 6px;
        cursor: pointer;
        &--active {
            background: #ed516a;
        }
    }
    &__dotLast {
        margin-right: 0px;
    }
    &__arrow {
        width: 28px;
        height: 40px;
        position: absolute;
        top: 172px;
        cursor: pointer;
        display: none;
        &--left {
            background: url($icon) -5px -5px;
            left: 0;
            &:hover{
                background: url($icon) -5px -55px;
            }   
        }
        &--right {
            background: url($icon) -43px -5px;
            right: 0;
            &:hover{
                background: url($icon) -81px -5px;
            }   
        }
    }
}
```

最终编译成`.lucky__box {width: 1016px;position: relative;left: 0;}`一个个类名，而不是嵌套的形式。

另外推荐两个插件: 自动补全代码插件： SublimeCodeIntel 前端开发神器插件： Emmet

```
div.topNav>div.-item.itemfirst+div.-itme.itemRank|bem
```

输入这个sublime自动帮我们生成

```javascript
    <div class="topNav">
        <div class="topNav__item itemfirst"></div>
        <div class="topNav__item itemRank"></div>
    </div>
```

## css工作流

css规范帮助我们更好地命名，维护与提升性能，而css工作流可以帮助更有效率地开发我们的代码，并提升编程体验。

css工作流一般包含css前置处理器，如scss，stylus，less等，同时还有后置处理器，如postcss。

PostCSS 是一个 CSS 后处理器 框架，允许你通过 JavaScript 对 CSS 进行修改。

PostCSS 的功能：

- 有前缀的自动添加前缀 【autoprefixer】
- 添加一系列的IE版本，回退到IE8、IE9和IE10 【postcss-opacity、postcss-color-rgba-fallback】
- 为没有支持的属性添加will-change属性 【postcss-will-change】
- 甚至可以帮我们完成图片合并压缩

如何使用:

配合gulp，在gulpfile.js文件中引入相关插件

```javascript
var gulp = require('gulp'); 
var postcss = require('gulp-postcss');
var autoprefixer = require('autoprefixer');
var cssnext = require('cssnext');
var precss = require('precss');
var opacity = require('postcss-opacity');
var color_rgba_fallback = require('postcss-color-rgba-fallback');
var pseudoelements = require('postcss-pseudoelements');
var vmin = require('postcss-vmin');
var pixrem = require('pixrem');
var will_change = require('postcss-will-change');
gulp.watch('./src/*.css',['css'])

gulp.task('css', function(){
    var processors = [
        autoprefixer,
        cssnext,
        precss,
        opacity,
        color_rgba_fallback,
        pseudoelements,
        will_change,
        vmin,
        pixrem

    ];
    return gulp.src('./src/*.css')
    .pipe(postcss(processors))
    .pipe(gulp.dest('./dest'));
})
/**自动补全浏览器前缀**/
.autoprefixer{
    display: flex;
}
```

gulp css 如下：

```javascript
.autoprefixer{
    display: -webkit-box;
    display: -ms-flexbox;
    display: flex;
}
.rgbaFallback {
    background: rgba(0, 0, 0, 0.5);
}
.opactiyFallback {
    opacity: 0.5;
}
/**对不兼容rgba的浏览器进行回退**/
.rgbaFallback {
    background: #000000;
    background: rgba(0, 0, 0, 0.5);
}
/**对不兼容opacity的浏览器进行处理**/
.opactiyFallback {
    -ms-filter: "progid:DXImageTransform.Microsoft.Alpha(Opacity=50)";
    opacity: 0.5;
}
.remFallback::before {
    content: '';
    line-height: 1rem;
}
/**对不兼容：：的浏览器进行处理**/
.remFallback:before {
    content: '';
    line-height: 16px;
    line-height: 1rem;
}
/**对css3动画补全浏览器前缀**/
@keyframes animationExample {
    from {
        width: 0;
    }
    to {
        width: 100%;
    }
}
.animateThis {
    animation: animationExample 2s;
    display: flex;
}
@-webkit-keyframes animationExample {
    from {
        width: 0;
    }
    to {
        width: 100%;
    }
}

@keyframes animationExample {
    from {
        width: 0;
    }
    to {
        width: 100%;
    }
}
.animateThis {
    -webkit-animation: animationExample 2s;
            animation: animationExample 2s;
    display: -webkit-box;
    display: -ms-flexbox;
    display: flex;
}
```
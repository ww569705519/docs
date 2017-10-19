# 移动前端开发CSS规范
!>移动前端开发规范意在开发团队遵循和约定的代码书写，从而提高代码的规范性和可维护性。
## 语法

- 用 4 个 空格来代替制表符（tab） -- 这是唯一能保证在所有环境下获得一致展现的方法。
- 为选择器分组时，将单独的选择器单独放在一行, 即 多选择器规则之间必须换行
- 为了代码的易读性，在每个声明块的左花括号前添加一个空格。
- 声明块的右花括号应当单独成行。
- 每条声明语句的 : 后必须插入一个空格。
- 每条声明语句的 : 前 禁止插入空格。
- 为了获得更准确的错误报告，每条声明都应该独占一行。
- 所有声明语句都应当以分号结尾。最后一条声明语句后面的分号是可选的，但是，如果省略这个分号，你的代码可能更易出错。
- 对于以逗号分隔的属性值，每个逗号后面都应该插入一个空格（例如， box-shadow ）。
- 不要在 rgb() 、 rgba() 、 hsl() 、 hsla() 或 rect() 值的 内部 的逗号后面插入空格。这样利于从多个属性值（既加逗号也加空格）中区分多个颜色值（只加逗号，不加空格）。
- 对于属性值或颜色参数，省略小于 1 的小数前面的 0 （例如， .5 代替 0.5 ； -.5px 代替 -0.5px ）。
- 十六进制值应该全部小写，例如， #fff 。在扫描文档时，小写字符易于分辨，因为他们的形式更易于区分。
- 尽量使用简写形式的十六进制值，例如，用 #fff 代替 #ffffff 。
- 为选择器中的属性添加双引号，例如， input[type="text"] 。 只有在某些情况下是可选的 -，但是，为了代码的一致性，建议都加上双引号。
- 避免为 0 值指定单位，例如，用 margin: 0; 代替 margin: 0px; 。

```
/* Bad CSS */
.selector , .selector-secondary , .selector [ type = text ] {
 padding : 15px ;
 margin : 0px 0px 15px ;
 background-color : rgba ( 0 , 0 , 0 , 0 . 5 );
 box - shadow : 0px 1px 2px #CCC , inset 0 1px 0 #FFFFFF
}

/* Good CSS */
.selector ,
.selector-secondary ,
.selector [ type = "text" ] {
 padding : 15px ;
 margin-bottom : 15px ;
 background-color : rgba ( 0 , 0 , 0 ,. 5 );
 box - shadow : 0 1px 2px #ccc , inset 0 1px 0 #fff ;
}
```
##  CSS 声明顺序
?> 相关的属性声明应当归为一组，并按照下面的顺序排列：
1. Positioning / Layout 定位/布局
2. Box model 盒模型
3. Typographic 印刷 文字相关
4. Visual 背景等 可视化
5. Misc 混合 杂项

!>由于定位（positioning）可以从正常的文档流中移除元素，并且还能覆盖盒模型（box model）相关的样式，因此排在首位。盒模型排在第二位，因为它决定了组件的尺寸和位置。

- 定位相关, 常见的有： position, z-index, top, right, bottom, left, clip等

- 布局相关, 常见的 有： display,float, clear, visibility, overflow, overflow-x,overflow-y 等

- 盒模型相关, 常见的有：width height margin padding, border 等

- 其他属性只是影响组件的内部（inside）或者是不影响前两组属性，因此排在后面。

!>要注意的是, border-width是盒模型 Box model , 其他诸如border-style, border-color,border-radius都不属于盒模型, 而是 Visual . 为了性能考虑, 同时又不影响使用, border放在盒模型 Box model 的最后面的位置.

按照这样的顺序书写可见提升浏览器渲染dom的性能 .

简单举个例子，网页中的图片，如果没有设置width和height，在图片载入之前，他所占的空间为0，但是当他加载完毕之后，那块为0的空间突然被撑开了，这样会导致，他下面的元素重新排列和渲染，造成重绘（repaint）和回流（reflow）。我们在写css的时候，把元素的定位放在前头，首先让浏览器知道该元素是在文本流内还是外，具体在页面的哪个部位，接着让浏览器知道他们的宽度和高度，border等这些占用空间的属性，其他的属性都是在这个固定的区域内渲染的。

完整的属性列表及其排列顺序请参考 [Recess](http://twitter.github.io/recess/) 。

```
.declaration-order {

 /* Positioning */
 display : block ;
 overflow : auto;
 float : right ;
 position : absolute ;
 top : 0 ;
 right : 0 ;
 bottom : 0 ;
 left : 0 ;
 z-index : 100 ;

 /* Box-model */
 margin: 0;
 padding: 0;
 width : 100px ;
 height : 100px ;
 border : 1px solid #e5e5e5 ;
 border - radius : 3px ;

 /* Typography */
 font : normal 13px "Helvetica Neue" , sans-serif ;
 line-height : 1 . 5 ;
 color : #333 ;
 text-align : center ;

 /* Visual */
 background-color : #f5f5f5 ;

 /* Misc */
opacity : 1 ;
animation : fadeIn .2 s ;
}
```

## 不要使用 @import

与 `<link>` 标签相比， @import 指令要慢很多，不光增加了额外的请求次数，还会导致不可预料的问题。替代办法有以下几种：
- 使用多个 <link> 元素
- 通过 Sass 或 Less 类似的 CSS 预处理器将多个 CSS 文件编译为一个文件
- 通过 Rails、Jekyll 或其他系统中提供过 CSS 文件合并功能

```
<!-- Use link elements -->
<link rel= "stylesheet" href= "core.css" >

<!-- Avoid @imports -->
<style>
 @import url ( "more.css" ) ;
</style>
```
## 媒体查询（ Media query ）的位置
将媒体查询放在尽可能相关规则的附近。不要将他们打包放在一个单一样式文件中或者放在文档底部。如果你把他们分开了，将来只会被大家遗忘。下面给出一个典型的实例。
```
.element {
 ...
}
.element-avatar {
 ...
}
.element-selected {
 ...
}

@media ( min-width : 480px ) {
	.element {
	 ...
	}
	.element-avatar {
	 ...
	}
	.element-selected {
	 ...
	}
}
```
## 带前缀的属性
当使用特定厂商的带有前缀的属性时，通过缩进的方式，让每个属性的值在垂直方向对齐，这样便于多行编辑。

在 Textmate 中，使用 Text → Edit Each Line in Selection ( ⌃⌘ A)。在 Sublime Text 2 中，使用 Selection → Add Previous Line ( ⌃⇧ ↑) 和 Selection → Add Next Line ( ⌃⇧ ↓)。

带有 特定厂商 浏览器前缀的属性在上面, 不带浏览器前缀的属性在下面, 如

```
a {
     display : -webkit-box;
     display : -webkit-flex;
     display : flex
}

p {
     -webkit-box-sizing: content-box;
     -moz-box-sizing: content-box;
     box-sizing: content-box;
}


/* Prefixed properties */
.selector {
     - webkit - box - shadow : 0 1px 2px rgba ( 0 , 0 , 0 ,. 15 );
     box - shadow : 0 1px 2px rgba ( 0 , 0 , 0 ,. 15 );
}
```

## 简写形式的属性声明

!>在需要显示地设置所有值的情况下，应当尽量限制使用简写形式的属性声明。常见的滥用简写属性声明的情况如下：

- padding
- margin
- font
- background
- border
- border-radius

大部分情况下，我们不需要为简写形式的属性声明指定所有值。例如，HTML 的 heading 元素只需要设置上、下边距（margin）的值，因此，在必要的时候，只需覆盖这两个值就可以。过度使用简写形式的属性声明会导致代码混乱，并且会对属性值带来不必要的覆盖从而引起意外的副作用。

MDN（Mozilla Developer Network）上一片非常好的关于 shorthand properties 的文章，对于不太熟悉简写属性声明及其行为的用户很有用。

```
/* Bad example */
.element {
    margin : 0 0 10px ;
    background : red ;
    background : url("image.jpg") ;
    border - radius : 3px 3px 0 0 ;
}

/* Good example */
.element {
    margin-bottom : 10px ;
    background-color : red ;
    background-image : url("image.jpg") ;
    border-top - left - radius : 3px ;
    border-top - right - radius : 3px ;
}
```
## Less 和 Sass 中的嵌套
避免非必要的嵌套。这是因为虽然你可以使用嵌套，但是并不意味着应该使用嵌套。只有在必须将样式限制在父元素内（也就是后代选择器），并且存在多个需要嵌套的元素时才使用嵌套。

```
// Without nesting
.table > thead > tr > th {
     …
}
.table > thead > tr > td {
     …
}

// With nesting
.table > thead > tr {
     > th {
         …
     }
     > td {
         …
     }
}
```
## 注释
代码是由人编写并维护的。请确保你的代码能够自描述、注释良好并且易于他人理解。好的代码注释能够传达上下文关系和代码目的。不要简单地重申组件或 class 名称。

对于较长的注释，务必书写完整的句子；对于一般性注解，可以书写简洁的短语。

```
/* Bad example */
/* Modal header */
.modal-header {
     ...
}

/* Good example */
/* Wrapping element for .modal-title and .modal-close */
.modal-header {
     ...
}
```

## class 命名
- class 名称中只能出现小写字符和破折号（dashe）（不是下划线，也不是驼峰命名法）。破折号应当用于相关 class 的命名（类似于命名空间）（例如， .btn 和 .btn-danger ）。
- 避免过度任意的简写。 .btn 代表 button ，但是 .s 不能表达任何意思。
- 避免用关键字做类名
- class 名称应当尽可能短，并且意义明确。
- 使用有意义的名称。使用有组织的或目的明确的名称，不要使用表现形式（presentational）的名称。
- 基于最近的父 class 或基本（base） class 作为新 class 的前缀。
- 使用 .js-* class 来标识行为（与样式相对），并且不要将这些 class 包含到 CSS 文件中。

?> 在为 Sass 和 Less 变量命名是也可以参考上面列出的各项规范。
```
/* Bad example */
.t { ... }
.red { ... }
.header { ... }

/* Good example */
.tweet { ... }
.important { ... }
.tweet-header { ... }
```
## 选择器
!> 不要随意使用id。id 在 JS 是唯一的，不能多次使用，而使用 class 类选择器却可以重复使用，另外 id 的优先级优先与 class ，所以 id 应该按需使用，而不能滥用。
- 对于通用元素使用 class ，这样利于渲染性能的优化。
- 对于经常出现的组件，避免使用属性选择器（例如， [class^=”...”] ）。浏览器的性能会受到这些因素的影响。
- 选择器要尽可能短，并且尽量限制组成选择器的元素个数，建议不要超过 3 。
- 只有 在必要的时候才将 class 限制在最近的父元素内（也就是后代选择器）（例如，不使用带前缀的 class 时 – 前缀类似于命名空间）。

```
/* Bad example */
span { ... }
.page-container #stream .stream-item .tweet .tweet-header .username { ... }
.avatar { ... }

/* Good example */
.avatar { ... }
.tweet-header .username { ... }
.tweet .avatar { ... }
```

## 小图片（必须） sprite 合并
推荐使用svg sprite或 icon font

## 禁止使用行内（ inline ）样式
!> `<p style=”font-size: 12px; color: #FFFFFF”>W3Cfuns</p>`

像这样的行内样式，最好用一个class代替。又如要隐藏某个元素，可以给他加一个class
```
.hide {
    display: none;
}
```
尽量做到样式和结构分离~ 十五、 合理使用 reset.css 样式重置

不要引用无 用 的reset.css , 有些 reset.css使用”*”来重置所有元素样式, 不建议 这样做, 这样 会影响性能.

## 链接的样式，（务必）按照这个顺序来书写
```
a:link -> a:visited -> a:hover -> a:active
```
##  代码组织
- 以组件为单位组织代码段。
- 制定一致的注释规范。
- 使用一致的空白符将代码分隔成块，这样利于扫描较大的文档。
- 如果使用了多个 CSS 文件，将其按照组件而非页面的形式分拆，因为页面会被重组，而组件只会被移动

```
/*
* Component section heading
*/
.element { ... }

/*
* Component section heading
*
* Sometimes you need to include optional context for the entire component. Do that up here if it’s important enough.
*/
.element { ... }

/* Contextual sub-component or XXX oolean XXX */
.element-heading { ... }
```
## 编辑器配置
将你的编辑器按照下面的配置进行设置，以避免常见的代码不一致和差异：
- 用 4 个 空格代替制表符（soft-tab 即用空格代表 tab 符）。
- 保存文件时，删除尾部的空白符。
- 设置文件编码为 UTF-8。
- 在文件结尾添加一个空白行
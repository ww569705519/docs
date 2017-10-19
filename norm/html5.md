# 移动前端开发HTML5规范
移动前端开发规范意在开发团队遵循和约定的代码书写，从而提高代码的规范性和可维护性。

## 编码
使用UTF-8编码。
```
<meta charset="UTF-8">
```
!> *不管有多少人共同参与同一项目，尽量保持每一行代码的可读性*
## HTML5 规范
### 语法
- 用4 个 空格来代替制表符（tab） -- 这是唯一能保证在所有环境下获得一致展现的方法。
- 嵌套元素应当缩进一次（即 4 个 空格）。
- 所有的代码都应是小写的，包括元素名称、属性，属性值（除非text或CDATA的内容）、选择器、css属性、属性值（字符串除外）
- 尾随空格是不必要的，容易搞复杂diff文件。这个绝对是经验教训的总结！！！
- 对于属性的定义，确保全部使用双引号，绝不要使用单引号。
- 不要在自闭合（self-closing）元素的尾部添加斜线 -- HTML5 规范 中明确说明这是可选的。
- 不要省略可选的结束标签（closing tag）（例如，`</li>`或 `</body>`）。

### HTML5 DOCTYPE
为每个 HTML 页面的第一行添加标准模式（standard mode）的声明，这样能够确保在每个浏览器中拥有一致的展现。
使用doctype 来启用标准模式，建议使用大写的 DOCTYPE
```
<!DOCTYPE html>
<html>
 <head>
 </head>
</html>
```
### head 内容
- Title标签必须设置为head的直接子元素，并紧随charset声明之后。
- Favicon保证可访问。
- Viewport属性必须指定。

### 语言属性
>  强烈建议为 html 根元素指定 lang 属性，从而为文档设置正确的语言。这将有助于语音合成工具确定其所应该采用的发音，有助于翻译工具确定其翻译时所应遵守的规则等等。

```
<html lang= "zh-CN" >
 <!-- ... -->
</html>
```
### 字符编码
通过明确声明字符编码，能够确保浏览器快速并容易的判断页面内容的渲染方式。这样做的好处是，可以避免在 HTML 中使用字符实体标记（character entity），从而全部与文档编码一致（一般采用 UTF-8 编码）。

页面必须指定字符编码的meta，且必须是 head 的第一个直接子元素，例： 。
```
<head>
 <meta charset= "UTF-8" >
</head>
```

### 引入 CSS 和 JavaScript 文件
根据 HTML5 规范，在引入 CSS 和 JavaScript 文件时一般不需要指定 type 属性，因为 text/css 和 text/javascript 分别是它们的默认值。
```
<!-- External CSS -->
<link rel= "stylesheet" href= "code-guide.css" >
<!-- In-document CSS -->
<style>
 /* ... */
</style>
<!-- JavaScript -->
<script src= "code-guide.js" ></script>
```
### 命名规则
- 属性必须使用小写字母，其属性值必须用双引号包围。
- 布尔类型的建议不添加属性值。自定义属性建议以 xxx- 为前缀，推荐使用data-。
- class 属性的命名 必须单词全字母小写，单词间以-分隔，且必须代表相应的模块或部件的内容或功能，不得以html内置样式进行命名，命名应该具有明确的语义。
- id属性必须保持在页面中的唯一性，命名应该具有明确的语义。

### 布尔（ boolean ）型属性
- 布尔型属性可以在声明时不赋值。XHTML 规范要求为其赋值，但是 HTML5 规范不需要。
- 更多信息请参考 [WhatWG section on boolean attributes]()
- 元素的布尔型属性如果有值，就是 true，如果没有值，就是 false。

*如果属性存在，其值必须是空字符串或 [...]属性的规范名称，并且不要再收尾添加空白符。简单来说，就是不用赋值。*

例如：
```
<input type= "text" disabled >

<input type= "checkbox" value= "1" checked >

<select>
 <option value= "1" selected > 1 </option>
</select>
```

### 标签
- Html中的标签名必须使用小写字母。
- 标签的闭合要符合html5的规定。
- 针对每个block，list或table元素另起一行，并在每个子元素前缩进。这样可读性好
- 标签的使用必须符合标签的嵌套规则，例：div不得置于p中，tbody必须置于table中。
- 在CSS可以实现相同需求的情况下不得使用表格table进行布局。

!> 标签的使用必须遵循标签的语义，例：
- p - 段落
- h1,h2,h3,h4,h5,h6 - 层级标题
- strong,em - 强调
- ins - 插入
- del - 删除
- abbr - 缩写
- code - 代码标识
- cite - 引述来源作品的标题
- q - 引用
- blockquote - 一段或长篇引用
- ul - 无序列表
- ol - 有序列表
- dl,dt,dd - 定义列表

### 减少标签的数量
编写 HTML 代码时，尽量避免多余的父元素。很多时候，这需要迭代和重构来实现。请看下面的案例：
```
<!-- Not so great -->
<span class= "avatar" >
 <img src= "..." >
</span>

<!-- Better -->
<img class= "avatar" src= "..." >
```
### JavaScript 生成的标签
通过 JavaScript 生成的标签让内容变得不易查找、编辑，并且降低性能。能避免时尽量避免。
### 图片
- 禁止 img 的src取值为空，延迟加载的图片也要增加默认的 src属性值。
- 建议重要图片添加 alt 属性值。
- 建议添加 width 和 height 属性以避免页面抖动。
- 有些特殊情况需要添加onerror事件, 如 `<img src="xxx.jpg" onerror="this.src='xxx.jpg'" /> 或 <img src="xxx.jpg" onerror="window.location.href='http://xxx/'" />`

### 语义性
根据目的来合理使用HTML，这点对于HTML5而言尤为重要。对于HTML5而言，例如header、footer、nav、section等跟div能实现的功能基本类似，但是语义性上有着天壤之别。

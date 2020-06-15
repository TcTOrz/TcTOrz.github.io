---
layout: post
title: Css面试题
categories: [css, 面试]
description: 自己遇见的一些题目
keywords: Css, interview
topmost: false
---

## 目录

- [CSS 选择器的优先级是如何计算的？](#css-选择器的优先级是如何计算的)
- [重置（resetting）CSS 和 标准化（normalizing）CSS 的区别是什么？你会选择哪种方式，为什么？](#重置resettingCSS-和-标准化normalizingCSS-的区别是什么你会选择哪种方式为什么)
- [请阐述`Float`定位的工作原理。](#请阐述float定位的工作原理)

### CSS 选择器的优先级是如何计算的？
浏览器通过优先级规则，判断元素展示哪些样式。优先级通过 4 个维度指标确定，我们假定以`a、b、c、d`命名，分别代表以下含义：
1. `a`表示是否使用内联样式（inline style）。如果使用，`a`为 1，否则为 0。
2. `b`表示 ID 选择器的数量。
3. `c`表示类选择器、属性选择器和伪类选择器数量之和。
4. `d`表示标签（类型）选择器和伪元素选择器之和。

优先级的结果并非通过以上四个值生成一个得分，而是每个值分开比较。`a、b、c、d`权重从左到右，依次减小。判断优先级时，从左到右，一一比较，直到比较出最大值，即可停止。所以，如果`b`的值不同，那么`c`和`d`不管多大，都不会对结果产生影响。比如`0，1，0，0`的优先级高于`0，0，10，10`。

当出现优先级相等的情况时，最晚出现的样式规则会被采纳。如果你在样式表里写了相同的规则（无论是在该文件内部还是其它样式文件中），那么最后出现的（在文件底部的）样式优先级更高，因此会被采纳。

在写样式时，我会使用较低的优先级，这样这些样式可以轻易地覆盖掉。尤其对写 UI 组件的时候更为重要，这样使用者就不需要通过非常复杂的优先级规则或使用`!important`的方式，去覆盖组件的样式了。

> 注：《CSS权威指南》 3-1节 106页 清晰说明：

> 选择符特指度由选择符本身组成部分决定。一个特指度由四部分构成，例如 0,0,0,0。选择符特指度通过下述规则确定：
- 选择符中的每个ID属性值加 0,1,0,0。
- 选择符中的每个类属性值、属性选择或伪类加 0,0,1,0。
- 选择符中的每个元素和伪元素加 0,0,0,1。伪类到底有没有特指度在css2中表述的有些自相矛盾，不过css2.1明确指出，伪元素有特指度。
- 连结符和通用选择符不增加特指度。

> 例如， 下面给出几个规则的特指度：

```css
h1 {color: red;}  /* 特指度： 0，0，0，1 */
p em { color: purple; } /* 特指度： 0，0，0，2 */
.grape { color: yellow; } /* 特指度： 0，0，1，0 */
*.bringht { color: black; } /* 特指度：0，0，1，0 */
p.bringht em.dark { color: white; } /* 特指度：0，0，2，2 */
#id216 { color: grey; } /* 特指度：0，1，0，0 */
div#sidebar *[href] { color: green; } /* 特指度：0，1，1，1 */
```

```css
h1 { color: red; } /* 0,0,0,1 */
body h1 { color: green; } /* 0,0,0,2 (胜出)*/

h2.grape { color: red; } /* 0,0,1,1 (胜出) */
h2 { color: green; } /* 0,0,0,1 */

html>body table tr[id="totals"] td ul>li { color: red; } /* 0, 0, 1, 7 */
li#answer {  color: green; } /* 0, 1, 0, 1(胜出) */
```

```css
h1 { color: silver; background: black; }
/* 将上面css打散成独立规则 */
h1 { color: silver; } /* 0,0,0,1 */
h1 { background: black; } /* 0,0,0,1 */

h1, h2.section { color: silver; background: black; }
/* 打散 */
h1 { color: silver; } /* 0,0,0,1 */
h1 { background: black; } /* 0,0,0,1 */
h2.section { color: silver; } /* 0,0,1,1 */
h2.section { background: black; } /* 0,0,1,1 */
```

```css
/* 通用选择符不增加特指度 */
div p { color: black; } /* 0,0,0,2 */
* { color: grey; } /* 0,0,0,0 */
body * strong { color: red; } /* 0,0,0,2 */
```

```html
<!-- 行内选择符特指度最高 -->
<h1 style="color: green;">test</h1> <!-- 1,0,0,0 -->
```

另外重要声明`!important`对特指度没有影响，但是会与不重要声明分开处理。重要声明和非重要声明冲突时，重要声明始终胜出。  

参考
- <https://www.smashingmagazine.com/2007/07/css-specificity-things-you-should-know/>
- <https://www.sitepoint.com/web-foundations/specificity/>

[[↑] 回到顶部](#目录)

### 重置（resetting）CSS 和 标准化（normalizing）CSS 的区别是什么？你会选择哪种方式，为什么？
- *重置（Resetting）*： 重置意味着除去所有的浏览器默认样式。对于页面所有的元素，像`margin`、`padding`、`font-size`这些样式全部置成一样。你将必须重新定义各种元素的样式。
- *标准化（Normalizing）*： 标准化没有去掉所有的默认样式，而是保留了有用的一部分，同时还纠正了一些常见错误。
当需要实现非常个性化的网页设计时，我会选择重置的方式，因为我要写很多自定义的样式以满足设计需求，这时候就不再需要标准化的默认样式了。  

参考
- <https://stackoverflow.com/questions/6887336/what-is-the-difference-between-normalize-css-and-reset-css>

[[↑] 回到顶部](#目录)

### 请阐述`Float`定位的工作原理。
浮动（float）是 CSS 定位属性。浮动元素从网页的正常流动中移出，但是保持了部分的流动性，会影响其他元素的定位（比如文字会围绕着浮动元素）。这一点与绝对定位不同，绝对定位的元素完全从文档流中脱离。

CSS 的`clear`属性通过使用`left`、`right`、`both`，让该元素向下移动（清除浮动）到浮动元素下面。

如果父元素只包含浮动元素，那么该父元素的高度将塌缩为 0。我们可以通过清除（clear）从浮动元素后到父元素关闭前之间的浮动来修复这个问题。

有一种 hack 的方法，是自定义一个`.clearfix`类，利用伪元素选择器`::after`清除浮动。[另外还有一些方法](https://css-tricks.com/all-about-floats/#article-header-id-4)，比如添加空的`<div></div>`和设置浮动元素父元素的`overflow`属性。与这些方法不同的是，`clearfix`方法，只需要给父元素添加一个类，定义如下：

```css
.clearfix::after {
  content: '';
  display: block;
  clear: both;
}
```

值得一提的是，把父元素属性设置为`overflow: auto`或`overflow: hidden`，会使其内部的子元素形成块格式化上下文（Block Formatting Context），并且父元素会扩张自己，使其能够包围它的子元素。

参考
- <https://css-tricks.com/all-about-floats/>

[[↑] 回到顶部](#目录)

写在最后：
- 原作者[yangshun](https://github.com/yangshun)
- 部分摘录自《CSS权威指南》(第四版)
- 感谢他们的付出!
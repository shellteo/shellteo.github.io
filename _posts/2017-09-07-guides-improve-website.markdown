---
layout: post
title:  "10种提高网站可访问性的方法"
date:   2017-09-07
categories: Web
author: 张翔
location: ShangHai, China
cover: "https://meetsup.oss-cn-hangzhou.aliyuncs.com/blog-images/article7/5b5be00508ba7.png"
description: 我的翻译计划第四篇，本篇文章介绍了10种可以提高网站可访问性的方法，可以让你网站对任何人都可访问。
---
---
我们列出了可以提高10种网站可访问性的方法，可以让你网站对任何人都可访问，包括残疾人。

下面是引用自W3C董事和万维网创建者——Tim Berners-Lee，“网络的力量在于它的普遍性”。作为制作网站谋生的人，我们有责任确保每个人都能访问我们的网站。Web可访问性听起来似乎很难，但做起来比它听起来容易得多。

我们的Web可访问性指南对所有网站都是通用的。

这不仅能帮助屏幕阅读器用户，而且还能提高网速慢时的用户体验。我们已经按照实施时间对我们的指南进行了排序，以便你能清楚地了解这个过程需要花费的时间。在你不知道值不值得之前，我可以告诉你 - 这完全是值得的。

第一件事：

## 什么是Web可访问性？
根据W3C标准，web可访问性意味着所有人对web都能感知，理解，浏览，交互以及贡献。从这方面来看，网站可访问性包括访问网络的所有条件，包括视觉，听觉，身体，言语，认知和神经障碍。

你可以在网络上找到一些关于这个主题的内容，如果你对这个方面感兴趣，应该更深入地了解**Web可访问性计划（WAI）**。

以上，我们的准则是：


## 1. 不依赖于颜色 (大约45分钟)
颜色经常是我们在网络上用来表达情感和传达信息的强大工具。但是，**我们不应该通过颜色传递信息给用户**。

为什么呢？

举个例子，众所周知，绿色代表“正确”，红色代表“错误”，但是当我们使用它作为唯一传递信息的工具时会发什么呢？

![web-accessibility-color-blindness-1.png](https://meetsup.oss-cn-hangzhou.aliyuncs.com/blog-images/article7/web-accessibility-color-blindness-1.png)

<font color='#cccccc'>色盲是最常见的视力缺陷之一。它影响了全球4.5％的人口（超过IE11用户，您知道...）。</font>

如果我们在界面上只使用颜色来展示重要的信息消息，那么我们就会失去这4.5%的用户。

颜色应该作为补充错误或确认的消息，但它不能作为我们唯一使用的工具。为了确保覆盖所有用户，我们应该添加标签或图标，用以显示表单中填写的信息是正确还是错误的。

![web-accessibility-color-blindness-2.png](https://meetsup.oss-cn-hangzhou.aliyuncs.com/blog-images/article7/web-accessibility-color-blindness-2.png)

ccaniuse.com采用了一个非常有趣的解决方案，该解决方案提供了一种替代调色板来显示兼容性表的内容。

![web-accessibility-caniuse-1.gif](https://meetsup.oss-cn-hangzhou.aliyuncs.com/blog-images/article7/web-accessibility-caniuse-1.gif)

非常适合在设计时检查色盲和对比度。为了确保你或者你的设计团队使用正确的工具，们强烈推荐用于Sketch的Stark插件，可以帮助你设计可访问性！

## 2. 不要禁用放大 (大约5分钟）

响应式设计的时代下，我们可能会犯一些不负责任的错误。

其中之一就是maximum-scale=1.0，它禁用了移动设备放大网页的功能。

```html
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1">
```

散光影响欧洲和亚洲30-60％的成年人，但视力模糊会影响到所有年龄和国籍的人。

**区块放大不仅仅是WCAG的一个指南，更是简化这些人的日常生活的工具**。所以下一次你在设计一个可访问性的网站的时候记得那些散光用户。

除了可以让用户在移动设备上自由缩放之外，还需要注意的是你的布局在桌面浏览器的200％下看起来也是好的。

## 3.重新发现alt属性（大约45分钟）

不管你做网站多长时间，你都可能会惊讶地发现一些关于**著名又神秘的alt属性**的技巧。

1. 每个img标签都有alt属性，但alt属性可以为空。如果图片是装饰性的或不需要表达页面的内容的，可以使用alt =""
2. 屏幕阅读器告诉用户，一个`<img>`标签就是一个图像，所以没有必要再去设置alt为“Picture of”；只需要直入主题。
3. 图像的功能和它的意义一样重要：如果你的logo链接到你的网站主页，那么您的alt文本应该是“Home Page”而不是“Logo”。
4. alt文本不仅仅是可访问性。有时网速慢的用户可以禁用图像能实现更快的用户体验，每当你写alt属性时，都需要考虑这些用户！

但是你的网站不是所有图片都使用img标签。可能会使用一两个SVG或者一整套SVG图标。

如何使所有人都可以访问SVG呢？幸运的是，可扩展矢量图形（SVG）标准已经提供了解决办法，我们可以使用`<title>`和`<desc>`标签来描述我们的矢量图！

```html
<symbol id="langIcon">
    <title>Language Icon</title>
    <desc>Longer description</desc>
    <path d="M0 2C6.47 2 2 6.48 2 12s4.47 10 9.99 0h24v24H0z" />
</symbol>
```

## 4.在视频中添加字幕和标题（4个多小时）

这可能是WCAG中最难实现的一项，不是因为技术上困难，而是因为它本身就很耗时。有几种方法可以完成这项工作：

1. 我们以YouTube为例。将视频上传到平台后，你可以启用字幕。字幕是自动生成的，但是在某些情况下可能会不准确，这取决于语言、背景噪音或演讲者的口音。然而，这些都很容易实现，并且可以在大多数讲英语视频中运行良好。

2. 如果我们正在寻找100％准确的字幕，这很难通过YouTube来获取，所以我们必须自己制作字幕或雇用第三方来做到这一点。YouTube采用最常见的字幕格式（.srt、.sub和.sbv），并让我们在平台上制作字幕，如果我们没有字幕软件，或者我们希望社区帮助我们翻译内容，而不需要管理员访问我们的帐户，这就会很方便。

![web-accessibility-translating-subtitles-youtube-tool-1.png](https://meetsup.oss-cn-hangzhou.aliyuncs.com/blog-images/article7/web-accessibility-translating-subtitles-youtube-tool-1.png)

3. 也许你不想使用YouTube作为托管平台。也许你希望使用服务器上托管的HTML5视频。这点我们已经解决了！ HTML5包含`<track>`标签，可以轻松地使用WebVTT格式（翻译FTW！）轻松地附加尽可能多的字幕以及文件。

```html
<video controls>
<source src="movie.mp4" type="video/mp4">
<track label="English Captions" kind="captions" srclang="eN" src="captions.vtt" default>
<track label="Subtitulos en español" kind="captions" srclang="es" src="subs.vtt">
</video>
```

## 5.语义化=可访问（大约45分钟）
记得字体标签吗？我希望你不要记住，那些是黑暗的年代。
普遍认为语义化不是随着HTML5而出现的。自从第一个HTML页面以来，语义化就一直存在，随后被大大的改善。使用HTML5标准，为日常使用引入了新的语义标签。

![web-accessibility-first-website.png](https://meetsup.oss-cn-hangzhou.aliyuncs.com/blog-images/article7/web-accessibility-first-website.png)

好的，但是语义化仅仅是为了SEO（搜索引擎优化）吗？

那可未必。当你有意识地使用`<h1>`标签取代`<p>`或`<span>`，你故意更改元素的含义，提供层次结构，并构建页面信息的树结构。
屏幕阅读器不会忘记这一点。事实上，语义化是最有用的武器之一。

请记住**能力越大责任越大**，从h1到新的主标签确保为每一个元素设置正确的语义标签。


## 6.使用正确的标记（大约30分钟）

As a follow up to the previous point I’d like to discuss a few false friends and controversial pairs:
(作为跟随之前的一点，我想讨论一些虚假的朋友和有争议的对：)

### Time vs. Datetime

时间元素有许多类型的格式、时区以及周期，使用ISO 8601标准来表示日期和时间。

Datetime是一个可选属性，有助于表示`<time>`的内容。我们来看一些例子：

```html
<time>14:54</time> Hours and minutes
<time>2018-06</time> Year and month
<time>-03:00</time> Time zones
<time>2h 32m</time> Harry Potter 2 Duration
<p>CSSConf Argentina took place on <time datetime=”2016-08-07”>August 7th</time></p>
```

### Del 和 Ins
web不断变化，但是我们不能忽视这些变化。我们可以使用ins和del的HTML标签与datetime属性组合来标记一些编辑的变化。

ins元素代表对文档的补充：

```html
<ul>
  <li><ins datetime="2017-08-02">Icecream</ins></li>
  <li>Candy</li>
  <li>Pasta</li>
</ul>
```

del元素代表删除的内容

```html
<ul>
  <li><del datetime="2017-06-05">Rewatch Harry Potter 8</del></li>
  <li><del datetime="2017-06-05">Cry because ____ dies.</del></li>
  <li><del datetime="2017-06-06">Write article</del></li>
  <li>Order room</li>
</ul>
```

### Button vs. a标签

这一点很棒，需要仔细理解，我们应该在什么时候使用它们？
`<a>`标签意味着从一个文件链接到另一个文件或者打开一个新标签或者在当前页面打开标签。然而，当我们希望触发一个动作像汉堡包菜单或图像画廊时，a标签不是最合适的。对上面的情况来说，button才是正确的选择，通常也可以使用JavaScript来实现。
此外，button标签容易与input的type="button"混淆，但是两者的差异在于前者依赖于表单能够设置更多内容（文本，图像文本或仅图像）。
使用button标签的时候需要考虑下面两件事：
首先，如果按钮的内容含义不够明确（例如，在关闭按钮中的“X”），我们必须添加一个aria-label属性来帮助解释该功能。

```html
<button aria-label="Close">X</button>
```

第二，如果添加href属性是有意义的（搜索组件或Lightbox库），那么我们也可以使用一个标签，并用JavaScript覆盖链接行为。如果未启用JavaScript，则使用href标签的图像库将会优雅降级。

## 7.必要时使用roles(大约1小时)
为了告诉屏幕阅读器用户我们的链接触发一个动作，实际上并不是普通的`<a>`标签，我们必须添加一个值为“button”的role属性。
但要小心！
编写JavaScript时，你不仅需要在点击时调用函数，而且在用户按空格时也需要调用函数。这是必需的，因为用于按钮的行为与用于链接的行为不同，用户应该能够触发这些命令的操作。

```html
<a href="img/kitten.jpg" role="button" onclick="handleBtnClick(event)" onKeyPress="handleBtnKeyPress(event)">
  Button
</a>

function handleBtnClick(event) {
    // Do something
}

function handleBtnKeyPress(event) {
    // Check to see if space or enter were pressed
    if (event.keyCode === 32 || event.keyCode === 13) {
        // Prevent the default action to stop scrolling when space is pressed
        event.preventDefault();
        // Do something
    }
}
```

在MDN上阅读更多信息。
记住，除非你违反规则，否则aria roles通常不是必需的，就像上面的例子一样。HTML语义元素已应用了默认role：`<nav>`标签的“导航”，`<a>`标签的“链接”等。这意味着只有当我们希望更改这些默认值时，才需要使用role属性。

## 8.隐藏元素（大约1小时）
HTML和CSS有几种方式来隐藏元素。下面的表格会帮助你找到最适合每种情况的方案：

|    方法    | 视图中的行为 |屏幕阅读器中的行为|兼容性|
| ---------- | ------------ | ---------------- | ---- |
| CSS: visibility: hidden; |   从视图隐藏元素，但其原始空间仍然存在（类似opacity：0;） |不可读|良好|
| CSS: display: none;   |  从视图中隐藏元素，原始空间丢失，流中的下一个元素将占据它的位置。 |不可读|良好|
| HTML5: hidden attribute  |  和display:none一样 |不可读|IE11以上|
| Aria-hidden ="true"  |  内容显示在浏览器中，但不通过辅助技术传达给用户 |不可读|IE11以上|
|CSS: .visuallyHidden class|   从视图中隐藏元素，并将其从工作流中删除 |可读|良好|

如果你想隐藏元素，但仍然想让屏幕阅读者知道，那么最后一个选项是最合适的。这在表单标签或跳到内容的链接中非常有用。visualHidden class应该是进入收藏夹的CSS代码之一，所以可以在所有项目中使用。是的，如果你喜欢你可以改变名字（我的建议是.pottersCloak，供你参考）

```css
.visually-hidden {
    position: absolute !important;
    clip: rect(1px 1px 1px 1px); /* IE6, IE7 */
    clip: rect(1px, 1px, 1px, 1px);
    padding:0 !important;
    border:0 !important;
    height: 1px !important;
    width: 1px !important;
    overflow: hidden;
}
body:hover .visually-hidden a,
body:hover .visually-hidden input,
body:hover .visually-hidden button {
    display: none !important;
}
```

## 9.遵循web可访问性标准（每周约30分钟）
web可访问性很难，标准和指南在这里会很有帮助。

本文中以前的所有内容都能引导你：`<button>`怎么工作的？我们什么时候使用它？display:none和hidden属性有什么不同？

一开始可能会很枯燥，但W3C标准和WCAG指南不仅可靠，而且还具有教育意义。继续前进，迷失在他们提供的无限信息中吧。我向你保证，你会发现你永远不知道的代码和技巧！

## 10.审核和审查（大约3小时）
一旦用到了以上所有知识，现在是测试它的时候了。以下是审核网站可访问性的最佳工具列表：

* ChromeVox：适用于Mac和Windows用户，这个Chrome扩展程序可用于测试网站的屏幕阅读器。
* Chrome的可用性开发工具：这是浏览器的另一个很好的扩展，在你日常开发人员工具中添加了辅助功能审核选项
* 颜色过滤器：这个工具可以测试你网站的不同类型的色盲颜色。
* W3C验证器：这个官方的W3C工具将让您知道您的HTML标记是否遵循Web可用性功能原则！
* A11Y合规平台：互联网可访问性局（BOIA）提供了一份分级报告，概述了当你使用WCAG A / AA检查点进行测试时的收费情况。
* WAVE：WebAIM制作的Web访问性评估工具。

## Aerolab的Web可访问性经验
我们尝试养成不断测试产品的习惯。我们的下一个产品应该始终会比上一个更好。是的，我们有时会犯错误，但是我们不断努力改进和适应，更别提学习挑战外的东西。
我们希望我们的产品能够为用户提供最好的体验，这就是为什么我们逐渐将可访问性标准纳入我们的工作流程。
还有一条漫长的道路，还有一些重要的改进空间，但是我们很高兴选择这条道路。
我们为Xapo做的登录页是我们如何应用网站可访问性标准的例子：

（Xapo网络和商店登录遵守指南的1,3,5,6,7和9条.它不适用2和7 100％，但是它在网页可访问性标准处于领先。）

![web-accessibility-xapo-network-landing-page.png](https://meetsup.oss-cn-hangzhou.aliyuncs.com/blog-images/article7/web-accessibility-xapo-network-landing-page.png)

## 最后的话
网站可访问性并不是那么易于实现，但如果您将其作为日常工作流程的一部分（而不是最后一分钟的清单），那么实施和测试将随着时间的推移变得更加容易。
如果你有疑问，不要害怕询问其他开发者或进行一些研究。我最喜欢的信息来源是 The A11y Project，A11y Wins，HTML5 Doctor和MDN。
**此外，如果您有任何更多的资源要推荐，请在评论区留言！**
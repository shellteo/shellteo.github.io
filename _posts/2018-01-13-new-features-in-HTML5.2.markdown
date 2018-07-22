---
layout: post
title:  "HTML5.2有哪些新特性？"
date:   2018-01-13
categories: BlockChain
author: 张翔
location: ShangHai, China
description: 介绍HTML5.2中的新元素、新写法以及一些无效的写法和删除的元素。
---
---
一个月之前，html5.2成为W3C的官方推荐（REC）。当一个规范到达官方推荐（REC）阶段时，这就意味着它已经得到了w3c成员和主任的认可，W3C建议用户部署，并且由网页开发人员实现。

在REC阶段，任何新的东西至少应该有两个独立的实现。这也就是我们网页开发人员开始使用新功能的好时机。

在HTML 5.2中，增加和删除了一些东西，所有这些都可以在官方的HTML 5.2更改页面上看到。在这篇文章中，我会回顾一些我认为会最大程度影响我们的变化。

## 1. 新特性

### 1.1 原生`<dialog>`元素

在所有的的HTML5.2的变化中，我对`<dialog>`元素（一个原生对话框）的引入最为兴奋。对话框在web上非常流行，但是每种实现方式都有所不同，导致网页上的大多数对话框的用户体验并不好。

新的`<dialog>`元素旨在改变这一点，提供了一种简单的方式来包含模态对话框，而不必担心其他一些缺点。我会单独写一篇关于这个元素是如何工作的详细的文章，以下是一些基础知识。

该对话框使用`<dialog>`元素创建：

```html
<dialog>  
  <h2>Dialog Title</h2>
  <p>Dialog content and other stuff will go here</p>
</dialog>  
```

默认情况下，除非应用了open属性，否则该对话框将从视图（并从dom访问）隐藏。

```html
<dialog open>
```

open属性可以通过调用show( )和close( )方法来切换，可以被**HTMLDialogElement**使用。

```html
<button id="open">Open Dialog</button>  
<button id="close">Close Dialog</button>
<dialog id="dialog">  
  <h2>Dialog Title</h2>
  <p>Dialog content and other stuff will go here</p>
</dialog>
<script>  
const dialog = document.getElementById("dialog");
document.getElementById("open").addEventListener("click", () => {  
  dialog.show();
});
document.getElementById("close").addEventListener("click", () => {  
  dialog.close();
});
</script>  
```

`<dialog>`元素已经在chrome中有支持，并且在firefox中也可以启用**dom.dialog_element.enabled**支持。


### 1.2 使用来自iframe的支付请求api

支付请求api是结算表单的本地化替代方案。通过将检索支付信息的处理移动到浏览器而不是在每个网站上的单独结算表格，它旨在为用户提供一种在网络上进行支付的标准化和一致化的方法。

在HTML 5.2之前，这些支付请求不能通过嵌入在文档中的iframe来完成。这使第三方嵌入式支付解决方案（例如stripe，paystack）基本上不可能利用这个api，因为他们的支付接口通常是在iframe中处理的。

html 5.2引入了**allowpaymentrequest**属性，当应用在iframe上时，允许在用户处于托管网页时使用支付请求api。

```html
<iframe allowpaymentrequest>
```

### 1.3 苹果图标的尺寸

要定义网页图标，可以在文档头部中使用 `<link rel="icon">`。要定义不同大小的图标，可以使用**sizes**属性。

```html
<link rel="icon" sizes="16x16" href="path/to/icon16.png">
<link rel="icon" sizes="32x32" href="path/to/icon32.png">
```

这个属性尽管只是建议，但是如果有多种尺寸的话，系统可以决定使用哪种尺寸的图标，特别是因为大多数设备都有自己的“最佳”图标尺寸。

在html 5.2之前，**sizes**属性只有在关联在**icon**上才有效。但是，苹果的ios设备不支持**sizes**属性。为了解决这个问题，苹果推出了一个设备专用关系，**appple-touch-icon**，可以用来定义设备上使用的图标。

在html5.2中，如果关系是**apple-touch-icon**，那么现在规范允许使用**sizes**属性，不再只是**icon**。我们就可以为不同的苹果设备提供不同大小的图标。虽然，据我所知，苹果设备仍然不支持**sizes**属性，但是这个属性以后会派上用场的。

## 2. 最近有效的写法

除了新的功能，HTML 5.2中已经可以使用一些以前无效的写法。

### 2.1 多个`<main>`元素

`<main>`元素表示网页的主要内容。虽然跨多个页面重复的内容可以放置在headers，sections或任何其他元素中，但`<main>`元素仅用于特定页面的特定内容。因此，在HTML 5.2之前，`<main>`元素必须在页面内唯一，才能使页面有效。

随着单页应用的普及，坚持这一规则是不合理的。在某些情况下，dom中有多个`<main>`元素，但是在给定的时间只有一个元素显示。

与HTML 5.2，现在可以可以说使用多个`<main>`元素，只要在给定的时间只有一个对用户可见。其他main元素必须使用**hidden**属性隐藏。

```html
<main>...</main>
<main hidden>...</main>
<main hidden>...</main>
```

正如我们所知，css中有几种方法可以用隐藏元素。但是,其他的`<main>`元素都必须使用hidden属性来隐藏。隐藏元素的任何其他方法，例如 **display: none;** 或者 **visibility: hidden;** 都将无效。

### 2.2 `<body>`中的样式

通常，使用`<style>`元素定义的内联css写在html文档的`<head>`内。随着组件化开发的推行，开发人员已经看到了把样式与其相关的html元素放在一起的好处。

在html 5.2中，在html文档的`<body>`中的任何地方嵌入的`<style>`块都是有效的。这意味着我们可以使样式更接近他们使用的地方。

```html
<body>  
    <p>I’m cornflowerblue!</p>
    <style>
        p { color: cornflowerblue; }
    </style>
    <p>I’m cornflowerblue!</p>
</body>  
```

然而，仍然可以注意到，**出于性能原因，样式应该优选地放置在`<head>`中**。根据规范，

> 样式元素应该优选地在文档的头部中使用。在文档的主体中使用样式可能会导致重新设置、触发布局或者导致重绘，因此应小心使用。

还应该注意的是，在上面的示例中，样式没有范围。稍后在html文档中定义的内联样式仍然适用于之前定义的元素，这就是为什么它可能会触发重绘。

### 2.3 `<legend>`中的标题

在表单中，`<legend>`元素表示`<fieldset>`中的表单字段的标题。在HTML 5.2之前，图例的内容必须是纯文本。现在，我们可以包含标题元素。

```html
<fieldset>  
    <legend><h2>Basic Information</h2></legend>
    <!-- Form fields for basic information -->
</fieldset>  
<fieldset>  
    <legend><h2>Contact Information</h2></legend>
    <!-- Form fields for contact information -->
</fieldset>  
```

当我们想要使用**fieldset**元素来对窗体的不同部分进行分组时，这是非常有用的。在这种情况下，使用标题是非常有意义的，这使得依赖文档大纲的用户可以更轻松地导航这些表单部分。

## 3. 已经删除的功能

在html 5.2中，删除了一些元素，即：

* **keygen**：用于帮助生成表单的公钥
* **menu**和**menuitem**：用于创建导航或上下文


## 4. 最近无效的写法

最后，开发中一些写法已经失效。

### 4.1 `<p>`中没有inline、floated或blocked的子元素

在html 5.2中，`<p>`元素的唯一有效子元素应该是内容。这意味着以下类型的元素不应再嵌套在一个段落中：

* 内联块
* 内联表
* 浮动和定位块

### 4.2 没有严格的文档类型

最后，我们可以告别以下严格的文件类型：
```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">;
```
```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">;
```
---
layout: post
title:  "[译] 为iPhone X设计网站"
date:   2017-10-09
categories: Web
author: 张翔
location: ShangHai, China
description: 开箱即用，Safari能在iPhone X的边缘完整显示你的网站。内容自动插入显示屏的安全区域，以免被圆角或设备的传感器外壳遮挡住。
---
---
开箱即用，Safari能在iPhone X的边缘完整显示你的网站。内容自动插入显示屏的安全区域，以免被圆角或设备的传感器外壳遮挡住。

插入区域填充了页面的`background-color`（如在`<body>`或`<html>`元素中指定），以便能与页面的其余部分进行混合。对很多网站来说，这些就足够了。如果您的页面上只有文本和图像上方的实体背景颜色，默认的插图将看起来不错。

其他页面 - 特别是那些设计有全宽水平导航栏的页面，如下面的页面 - 可以选择进一步，以充分利用新屏幕的功能。 `iPhone X人机界面指`南（`https://developer.apple.com/ios/human-interface-guidelines/overview/iphone-x/`）详细介绍了一些需要注意的一般设计原则，`UIKit文档`（`https://developer.apple.com/documentation/uikit/uiview/positioning_content_relative_to_the_safe_area`）讨论了原生应用程序可以采用的特定机制来确保它们看起来很好。您的网站可以利用iOS 11中引入的几个类似的新的WebKit API来充分利用显示器的边缘特性。


`https://webkit.org/demos/safe-area-insets/1-default.html`
Safari的默认插入页面的行为。

## 使用整个屏幕

第一个新功能是对现有的`viewport`元标签的扩展，名为`viewport-fit`，它提供对插入行为的控制。`viewport-fit`在iOS 11中可用。

`viewport-fit`的默认值为`auto`，这会导致自动排版。为了禁用该行为并使页面布局到屏幕的完整大小，您可以设置`viewport-fit`为`cover`。这样做之后，`viewport`元标签看起来像这样：

```html
<meta name='viewport' content='initial-scale=1, viewport-fit=cover'>
```

重新加载后，导航栏看起来好多了，扩展到屏幕两边。然而，我们清除为什么应该遵守系统的安全区域内容：某些页面的内容被设备的传感器外壳遮挡，导致底部的导航栏很难使用。

`https://webkit.org/demos/safe-area-insets/2-viewport-fit.html`
使用 `viewport-fit=cover`来覆盖整个屏幕。

## 遵守安全领域

在采用`viewport-fit = cover`之后，再次使页面可用的下一步是选择性地将padding应用于包含重要内容的元素，以确保它们不被屏幕的形状遮蔽。这能使一个页面充分利用iPhone X上增加的屏幕空间，同时动态调整，避免处于角落、传感器外壳和用于访问主屏幕的指示器。

https://webkit.org/demos/safe-area-insets/safe-areas.html
iPhone X在横向上安全和不安全的区域，插入的常数表示。

为了实现这一点，iOS 11中的WebKit包括一个新的CSS函数`constant()`和四个预定义的常量，`safe-area-inset-left`、`safe-area-inset-right`、`safe-area-inset-top`和`safe-area-inset-bottom`，当组合时，这些样式声明允许引用每一侧的安全区域的当前大小。

>CSS工作组最近决定添加此功能，但使用不同的名称。采用时请记住这一点。

`constant()`可以在任何`var()`的地方使用，例如，在`padding`属性内：

```css
.post {
  padding: 12px;
  padding-left: constant(safe-area-inset-left);
  padding-right: constant(safe-area-inset-right);
}
```

对于不支持`constant()`的浏览器，包含它的样式规则将被忽略；因此，重要的是使用`constant()`时如果不支持声明回退规则。

`https://webkit.org/demos/safe-area-insets/3-safe-area-constants.html`
遵守安全区域，使重要内容可见。

## 结合min()和max()一起使用

>这一部分介绍iOS 11当前未包含的功能。

如果您在网站设计中采用安全区域，您可能会注意到，除了安全区域之外，还需要指定一个最小的padding。在上面的页面中，我们用`constant(safe-area-inset-left)）`替换了12px左边的padding，当屏幕旋转回到纵向时，左边的安全区域变为0px，文本立即与屏幕边缘相邻。

`https://webkit.org/demos/safe-area-insets/3-safe-area-constants.html`
安全区域不能代替margins。

要解决这个问题，我们要指定padding为默认padding或是安全区域大小，以两者中的较大者为准。这可以通过最新的CSS函数`min()`和`max()`来实现，这会在未来的Safari版本中提供。两个函数都使用任意数量的参数并返回最小或最大值。它们可以在`calc()`内部使用，或者嵌套在对方内部，并且这两个函数都允许使用`calc()`，就像数学一样。

对于这种情况，我们应该使用`max()`：

```css
@supports(padding: max(0px)) {
  .post {
  padding-left: max(12px, constant(safe-area-inset-left));
  padding-right: max(12px, constant(safe-area-inset-right));
  }
}
```

>使用@supports来检测最小和最大值非常重要，因为并不是所有地方都支持，并且由于CSS处理了无效变量，所以不要在@supports查询中指定一个变量。

在我们的示例页面中，在纵向方向，`constant(safe-area-inset-left)`解析为0px，因此`max()`函数解析为12px。在风景中，当`constant(safe-area-inset-left)`由于传感器外壳而较大时，`max()`函数将会解析为该尺寸，以确保其内部的重要内容始终可见。

`https://webkit.org/demos/safe-area-insets/4-min-max.html`
使用max()将安全区域与传统的margins组合。

有经验的Web开发人员可能以前遇到过“CSS锁”机制，通常用于将CSS属性绑定到特定范围的值。同时使用`min()`和`max()`使得这更容易，并在以后非常有助于实现有效的响应设计。

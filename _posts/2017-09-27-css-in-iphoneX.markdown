---
layout: post
title:  "[译] iPhone X的“头帘儿”与CSS"
date:   2017-09-27
categories: CSS
author: 张翔
location: ShangHai, China
cover: "https://i.loli.net/2018/08/01/5b61bc34021e1.jpg"
description: 自从iPhone X发布，它的头帘儿就是开发者一直头疼的问题，本文给出了一种解决办法，如果想提前适配iPhone X，一定要看这篇文章！
---
---
苹果的iPhone X是全屏幕手机，但是为了给相机和其他组件腾出空间，iphone x有一个缺口。结果导致屏幕设计处于一个尴尬的局面，例如将网站限制在安全区域内，边缘为白色条纹。虽然删除它不是一个很有技巧的事，在body上加一**个background-color**就可以做到。或者扩展网站的整个区域，可以在meta viewport中添加viewport-fit = cover。

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover">
```

![Image 1.png](https://i.loli.net/2018/08/01/5b61bc310d0ba.png)

然后你需要了解常由安全区处理的重叠问题。有一些新的CSS属性可以帮助你。请看斯蒂芬·拉德福文档（http://stephenradford.me/removing-the-white-bars-in-safari-on-iphone-x/）：

> 在iOS 11的Safari版本上，使用viewport-fit = cover的时候，为了处理任何可能的调整，需要用到如下常量。
> 
> - safe-area-inset-top
> - safe-area-inset-right
> - safe-area-inset-left
> - safe-area-inset-bottom
> 
> 这可以添加到外边距（margin），内填充（padding）或绝对位置的值（absolute），如top或这left。
> 
> 我将以下内容添加到网站上的主容器中
> 
> ```css 
> padding: constant(safe-area-inset-top)
> constant(safe-area-inset-right) 
> constant(safe-area-inset-bottom)
> constant(safe-area-inset-left); 
> ```

如果在iphone x的缺口屏幕中，安全区域和固定位置又会有另一个非常尴尬的情况。在Darryl Pogue的报告中（https://ayogo.com/blog/ios11-viewport/）:

> iOS 11与早期版本不同之处在于Webview content现在遵守安全区域。这意味着如果你有一个标题栏，它是一个固定位置元素，它的顶部为0（top：0），它最初将会渲染在屏幕顶部20px以下：对齐到状态栏的底部。当您向下滚动时，它就会向上移动到状态栏的后面。当你向上滚动时，它将再次回到状态栏的下方（留下一个尴尬的间距，内容在20px的间距中显示）。
> 你可以在下面的图中看到这有多糟糕：

![GIF.gif](https://i.loli.net/2018/08/01/5b61bc7c6f456.gif)

幸运的是，只需要一些简单的修改，可以将**viewport-fit=cover**添加到meta viewport标签中来修复它。
如果你要覆盖这个视口，那么你可能需要一点点聪明才能避免隐藏的内容！

![GIF2.gif](https://i.loli.net/2018/08/01/5b61bc9138ce2.gif)
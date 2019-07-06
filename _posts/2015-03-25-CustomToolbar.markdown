---
layout: post
title:  "C#初学——CustomToolbar修改"
date:   2015-03-25
categories: C#
author: 张翔
location: NanChang, China
cover: ""
description: C#中CustomToolbar修改
---
---

如上图的的bar一栏想要深度定制的话，只能通过如下方法。
![img](http://myblog-images1.oss-cn-beijing.aliyuncs.com/csharp/1.png)
![img](http://myblog-images1.oss-cn-beijing.aliyuncs.com/csharp/2.png)

### 1. 打开Xamarin，新建unsupported-MonoMacProject
### 2. 用xcode打开MainWindow.xib文件，新建文件类名CustomToolbar继承NSView
### 3. 拖一个customview控件，继承CustomToolbar，再加入.h文件，必须要
`import <MainToolBar/MainToolBar.h>`，不然会报错。

### 4. 在MainWindow.cs文件里面加上如下代码
```C#
public override bool CanBecomeKeyWindow {
            get {
                return true;
            }
        }
        public override bool CanBecomeMainWindow {
            get {
                return true;
            }
        } 
```

### 5. 在MainWindowContorller.cs文件里加上如下代码

```C#
#region NSWindowDelegate
        public class WindowDelegate:NSWindowDelegate
        {
            CustomToolbar MainToolBar;
            public WindowDelegate(CustomToolbar _tool)
            {
                MainToolBar = _tool;
            }
            public override RectangleF WillPositionSheet(NSWindow window, NSWindow sheet, RectangleF usingRect)
            {
                return new RectangleF((MainToolBar.Frame.Size.Width - usingRect.Size.Width) / 2, MainToolBar.Frame.Location.Y, usingRect.Size.Width, usingRect.Size.Height);
            }
        }
        #endregion
        public override void AwakeFromNib ()
        {
            this.Window.Delegate = new WindowDelegate(this.MainToolBar);
        } 
```

### 6. 在CustomToolbar.cs里面加上

```C#
using System;
using MonoMac.Foundation;
using MonoMac.AppKit;
using System.Drawing;
using System.Collections.Generic; 


namespace toolbar
{
    public partial class CustomToolbar : NSView
    {
        private NSColor startingColor;
        private NSColor endingColor;
        private int angle;
        NSButton CloseButton;
        NSButton ZoomButton;
        NSButton MiniaturizeButton;
        List<NSButton> lstButton;
        public CustomToolbar (IntPtr handle) : base (handle)
        {
            Initialize ();
        }
        void Initialize ()
        {
            this.startingColor = NSColor.FromCalibratedWhite (0.85f, 1);
            this.endingColor =  NSColor.FromCalibratedWhite (0.7f, 1);
            this.angle = 270;
            CloseButton = NSWindow.StandardWindowButton(NSWindowButton.CloseButton, NSWindowStyle.NonactivatingPanel);
            CloseButton.Frame = new RectangleF(10, this.Frame.Height - CloseButton.Frame.Height, 15, 15);
            MiniaturizeButton = NSWindow.StandardWindowButton(NSWindowButton.MiniaturizeButton, NSWindowStyle.TexturedBackground);
            MiniaturizeButton.Frame = new RectangleF(30, this.Frame.Height - MiniaturizeButton.Frame.Height, 15, 15);
            ZoomButton = NSWindow.StandardWindowButton(NSWindowButton.ZoomButton, NSWindowStyle.TexturedBackground);
            ZoomButton.Frame = new RectangleF(50, this.Frame.Height - ZoomButton.Frame.Height, 15, 15);
            lstButton = new List<NSButton> { CloseButton, MiniaturizeButton, ZoomButton };
            for (int i = 0; i < lstButton.Count; i++)
            {
                NSButton button = lstButton[i];
                button.Tag = i;
                button.AbortEditing();
                button.Highlight(false);
                this.AddSubview(button);
                button.AddTrackingRect(button.Bounds, this, System.IntPtr.Zero, true);
                button.Activated += ThreeButtonClicked;
            }
        }
        void ThreeButtonClicked(object sender, EventArgs e)
        {
            NSButton button = sender as NSButton;
            button.Highlight(true);
            switch (button.Tag)
            {
            case 0:
                this.Window.Close();
                break;
            case 1:
                this.Window.Miniaturize(NSObject.FromObject(null));
                break;
            case 2:
                this.Window.Zoom(NSObject.FromObject(null));
                break; 
            default:
                break;
            }
        }
        public override void MouseEntered(NSEvent theEvent)
        {
            foreach (NSButton button in lstButton)
            {
                button.Highlight(true);
            }
        }
        public override void MouseExited(NSEvent theEvent)
        {
            foreach (NSButton button in lstButton)
            {
                button.Highlight(false);
            }
        }
        public override void DrawRect (System.Drawing.RectangleF dirtyRect)
        {
            if (endingColor == null || startingColor.Equals (endingColor)) {
                startingColor.Set();
            } else {
                NSGradient ag = new NSGradient (startingColor, endingColor);
                ag.DrawInRect (this.Bounds, this.angle);
            }
        }
        PointF initialLocation;
        //        bool NoDragged = false;
        //
        //        public override void MouseUp (NSEvent theEvent)
        //        {
        //            NoDragged = false;
        //        }
        public override void MouseDown (NSEvent theEvent)
        {
            this.initialLocation = theEvent.LocationInWindow;
        }
        public override void MouseDragged (NSEvent theEvent)
        {            
            //            if ((theEvent.LocationInWindow.X >MiddleModuleButtonRect.X) && (theEvent.LocationInWindow.X < MiddleModuleButtonRect.X + MiddleModuleButtonRect.Width)
            //
            //                && (theEvent.LocationInWindow.Y > (MainUIObject.MainWindow.Frame.Height - this.Frame.Height)) && theEvent.LocationInWindow.Y < MainUIObject.MainWindow.Frame.Height) {
            //                NoDragged = true;
            //            }
            //            if (NoDragged == true) 
            //            {
            //                return;
            //            }    
            RectangleF screenVisibleFrame = NSScreen.MainScreen.VisibleFrame;
            RectangleF windowFrame = Window.Frame;
            PointF newOrigin = windowFrame.Location;
            PointF currentLocation = theEvent.LocationInWindow;
            newOrigin.X += (currentLocation.X - initialLocation.X);
            newOrigin.Y += (currentLocation.Y - initialLocation.Y);
            if ((newOrigin.Y + windowFrame.Size.Height) > (screenVisibleFrame.Location.Y + screenVisibleFrame.Size.Height)) {
                newOrigin.Y = screenVisibleFrame.Location.Y + (screenVisibleFrame.Size.Height - windowFrame.Size.Height);
            }
            this.Window.SetFrameOrigin (newOrigin);
        }
    }
} 
```


#### C#中const与readonly区别
const 的概念就是一个包含不能修改的值的变量。
常数表达式是在编译时可被完全计算的表达式。因此不能从一个变量中提取的值来初始化常量。
如果 const int a = b+1;b是一个变量，显然不能再编译时就计算出结果，所以常量是不可以用变量来初始化的。

readonly 允许把一个字段设置成常量，但可以执行一些运算，可以确定它的初始值。
因为 readonly 是在计算时执行的，当然它可以用某些变量初始化。
readonly 是实例成员，所以不同的实例可以有不同的常量值，这使readonly更灵活。

readonly 关键字与 const 关键字不同。

1. const 字段只能在该字段的声明中初始化。
readonly 字段可以在声明或构造函数中初始化。因此，根据所使用的构造函数，readonly 字段可能具有不同的值。
2. const 字段是编译时常数，而 readonly 字段可用于运行时常数。
3. const 默认就是静态的，而 readonly 如果设置成静态的就必须显示声明。
4．const 对于引用类型的常数，可能的值只能是 string 和 null。
readonly可以是任何类型

总结
const只能在初期就使用常量初始化好。对于每一次编译后的结果，const的值是固定的，而readonly的值是可以在运行的时候才确定值的~~
#### C#中IList与List区别
首先IList 泛型接口是 ICollection 泛型接口的子代，并且是所有泛型列表的基接口。

它仅仅是所有泛型类型的接口，并没有太多方法可以方便实用，如果仅仅是作为集合数据的承载体，确实，IList<T>可以胜任。

不过，更多的时候，我们要对集合数据进行处理，从中筛选数据或者排序。这个时候IList<T>就爱莫能助了。

1、当你只想使用接口的方法时,ILis<>这种方式比较好.他不获取实现这个接口的类的其他方法和字段，有效的节省空间．

2、IList <>是个接口,定义了一些操作方法这些方法要你自己去实现

List <>是泛型类,它已经实现了IList <>定义的那些方法

IList <Class1> IList11 =new List <Class1>();

List <Class1> List11 =new List <Class1>();

这两行代码，从操作上来看，实际上都是创建了一个List<Class1>对象的实例，也就是说，他们的操作没有区别。

只是用于保存这个操作的返回值变量类型不一样而已。

那么，我们可以这么理解，这两行代码的目的不一样。

List <Class1> List11 =new List <Class1>();

是想创建一个List<Class1>，而且需要使用到List<T>的功能，进行相关操作。

而

IList <Class1> IList11 =new List <Class1>();

只是想创建一个基于接口IList<Class1>的对象的实例，只是这个接口是由List<T>实现的。所以它只是希望使用到IList<T>接口规定的功能而已
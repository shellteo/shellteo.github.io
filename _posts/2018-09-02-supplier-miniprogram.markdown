---
layout: post
title:  "供应商小程序记录"
date:   2018-09-02
categories: js
author: 张翔
location: ShangHai, China
cover: ""
description: 最近公司有个组需要做一个小程序，要实现的一些效果和功能，本质上不难，这里做一个记录以备忘。
---
---
最近公司有个组需要做一个小程序，要实现的一些效果和功能，本质上不难，这里做一个记录以备忘。

## 一、下拉刷新上拉加载
实现下拉刷新和上拉加载的效果，虽然微信提供了**wx.startPullDownRefresh**方法，但是那是从页面最顶部实现的下拉刷新，不符合这里面的需求，所以这里实现了一个下拉刷新，上拉加载更多使用的是监听：**bindscrolltolower**。
如果需求可以妥协，还是建议使用微信自带的**onPullDownRefresh()**【监听用户下拉刷新事件】和**onReachBottom()**【监听用户上拉触底事件】。

![1.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/paidan/1.png)![2.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/paidan/2.png)

wxml片段，主要是这个布局的实现和下拉刷新的wxml代码和注释
```html
<template>
  <view class="homePage">
  <!-- 页面最上面的退出栏，不用时隐藏 -->
    <view class="logoutView {{showHeaderText?'':'hideHeaderText'}}">
      <view class="helpText">欢迎</view>
      <view>欢迎使用xxx小程序</view>
      <view class="logout" @tap="logout">退出</view>
    </view>
    <!-- 页面上面的的3个tab -->
    <view class="homeHeader">
      <view class="{{current == 0?'btmBor':''}} {{grabOrderReminderCount>0?'notice':''}}" catchtap="switchSwiper('current',0)">tab1</view>
      <view class="{{current == 1?'btmBor':''}} {{deliveryList.length>0?'notice':''}}" catchtap="switchSwiper('current',1)">tab2</view>
      <view class="{{current == 2?'btmBor':''}} {{myOrderReminderCount>0?'notice':''}}" catchtap="switchSwiper('current',2)">tab3</view>
      <!-- 可以展开最上面的退出栏-->
      <view class="arrowView" catchtap="toggleHeaderText">
        <image src="{{showHeaderText?'angle-double-up.svg':'angle-double-down.svg'}}" class="arrowIcon"></image>
      </view>
    </view>
    <!-- loading组件 -->
    <topLoading></topLoading>
    <!-- 3个tab页面分别使用小程序的swiper组件，3个tab -->
    <swiper class="swiperClass" current="{{current}}" bindchange="swiperChange('current')">
      <swiper-item>
          <!-- tab1滚动层 -->
        <scroll-view class="scroll" scroll-y="{{scrollY}}" bindscroll="scroll" bindscrolltolower="grabListBtm">
          <view style="transform:translate(0, {{deltaY}}px); transition:transform {{CloseTime}}s;" @touchstart="touchStart" @touchmove="touchMove" @touchend="toucheEnd">
            <repeat for="{{grabOrderList}}" key="index" index="index" item="item">
            <!-- tab1页面数据 -->
            </repeat>
            <!-- 页面底部loading加载 -->
            <block wx:if="{{!grabNoMoreData}}">
              <view class="btmLoading">
                <bottomLoading></bottomLoading>
              </view>
            </block>
            <!-- 页面底部没有更多数据 -->
            <block wx:else>
              <view class="noMoreData">没有更多数据</view>
            </block>
          </view>
        </scroll-view>
      </swiper-item>
      <swiper-item>
          <!-- tab2滚动层 -->
        <scroll-view class="scroll" scroll-y="{{scrollY}}" bindscroll="scroll" bindscrolltolower="deliveryListBtm" scroll-top="deliveryScrollTop">
          <view style="transform:translate(0, {{deltaY}}px); transition:transform {{CloseTime}}s;" @touchstart="touchStart" @touchmove="touchMove" @touchend="toucheEnd">
            <repeat for="{{deliveryList}}" key="index" index="index" item="item">
            <!-- tab2页面数据 -->
            </repeat>
            <!-- 页面底部loading加载 -->
            <block wx:if="{{!deliveryNoMoreData}}">
              <view class="btmLoading">
                <bottomLoading></bottomLoading>
              </view>
            </block>
            <!-- 页面底部没有更多数据 -->
            <block wx:else>
              <view class="noMoreData">没有更多数据</view>
            </block>
          </view>
        </scroll-view>
      </swiper-item>
      <swiper-item>
          <!-- tab3滚动层，滚动的监听函数和滚动到底部的监听函数 -->
        <scroll-view class="scroll" scroll-y="{{scrollY}}" bindscroll="scroll" bindscrolltolower="myOrderListBtm">
            <view style="transform:translate(0, {{deltaY}}px); transition:transform {{CloseTime}}s;" @touchstart="touchStart" @touchmove="touchMove" @touchend="toucheEnd">
                <!-- tab3上面搜索栏目 -->
                <view class="orderHeader">
                  <input placeholder="请输入xxx搜索" class="searchInput" placeholder-class="placeholder" bindinput="myOrderIdInput" bindconfirm="searchMyOrderListById"/>
                  <view @tap="showFilter"><image class="filterImg" mode="aspectFit" src=""></image></view>
                </view>
                <repeat for="{{myOrderList}}" key="index" index="index" item="item">
                <!-- tab3页面数据 -->
                </repeat>
                <!-- 页面底部loading加载 -->
                <block wx:if="{{!myOrderNoMoreData}}">
                  <view class="btmLoading">
                    <bottomLoading></bottomLoading>
                  </view>
                </block>
                <!-- 页面底部没有更多数据 -->
                <block wx:else>
                  <view class="noMoreData">没有更多数据</view>
                </block>
              </view>
        </scroll-view>
        <!-- 遮罩层 -->
        <view class="mask" wx:if="{{filterShow}}" @tap="hideFilter"></view>
        <!-- 侧边弹出搜索栏 -->
        <view class="filterBox {{filterShow?'filterAnimation':''}}" style="height:{{filterHeight}}">
          <view class="filterContent">
          <!-- 侧边栏的搜索内容 -->
          </view>
          <!-- 侧边栏底部的重置确定按钮 -->
          <view class="btnBox">
            <view @tap="resetFilter">重置</view>
            <view @tap="confirmFilter">确定</view>
          </view>
        </view>
      </swiper-item>
    </swiper>
  </view>
</template>
```

javascript片段，主要是下拉刷新的方法代码
```javascript
<script>
    methods = {
        // 滑动开始
        touchStart(event) {
            // y方向上允许滚动，使用的是wx的scroll-y属性
            this.scrollY = true;
            // y方向的滑动起点
            this.startY = event.touches[0].pageY;
            // x方向的滑动起点
            this.startX = event.touches[0].pageX;
            // 重置关闭时间
            this.CloseTime = 0;
        },
        // 滑动过程
        touchMove(event) {
            // scrollTop是scroll-view滚动的距离，判断是不是向下滚动
            if (this.scrollTop > 5 && this.deltaY <= 0) {
              this.scrollY = true;
              return;
            }
            let moveX = event.touches[0].pageX;
            let moveY = event.touches[0].pageY;
            // x 与 y 方向的距离，判断是不是上下滚动，而不是左右滚动
            if (Math.abs(moveX - this.startX) / Math.abs(moveY - this.startY) > 0.5 || Math.abs(moveX - this.startX) > 50) return;
            // deltaY：y方向滑动距离
            this.deltaY = (moveY - this.startY)/3*2;
            this.scrollY = this.deltaY <= 0;
            // 滑动距离超过50就展示顶部loading
            if(this.deltaY >= 50) {
              this.$invoke('topLoading', 'show', {
                position: 'bottom'
              })
            }
        },
        // 滑动结束
        toucheEnd() {
            this.scrollY = true;
            // 如果滑动距离大于30就去请求数据，关闭loading
            if (this.deltaY > 30) {
              this.CloseTime = 1;
              this.deltaY = 0;
              this.$invoke('topLoading', 'close');
              // 请求列表中的数据
            } else {
              this.$invoke('topLoading', 'close');
              this.deltaY = 0;
            }
        },
        scroll(e) {
            this.scrollTop = e.detail.scrollTop;
        }
    }
</script>
```


wxss片段，主要的css和注释
```css
<style lang="less">
    /*整个页面的样式，flex布局*/
    .homePage{
        display: flex;
        flex-direction: column;
        align-items: stretch;
        justify-content: space-between;
        overflow: hidden;
        position: relative;
        background-color:#eeeeee;
        width:100%;
        height: 100%;
    }
    /*swiper的样式*/
    .swiperClass{
        height: 100%;
        width:100%;
        flex:1;
        overflow:hidden;
    }
    /*滚动的样式*/
    .scroll {
          height:100%;
          width:100%;
    }
    /*小红点提醒*/
    .notice::after{
        content: '·';
        display: inline-block;
        position: absolute;
        top: 0;
        font-size: 80rpx;
        color:@myRed;
    }
</style>
```

## 二、实现侧边栏弹出

![4.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/paidan/4.png)![3.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/paidan/3.png)

wxml片段
```html
<template>
  <view class="homePage">
    <!-- 3个tab页面分别使用小程序的swiper组件，3个tab -->
    <swiper class="swiperClass" current="{{current}}" bindchange="swiperChange('current')">
      <swiper-item>
          <!-- tab3滚动层，滚动的监听函数和滚动到底部的监听函数 -->
        <scroll-view class="scroll" scroll-y="{{scrollY}}" bindscroll="scroll" bindscrolltolower="myOrderListBtm">
        </scroll-view>
        <!-- 遮罩层 -->
        <view class="mask" wx:if="{{filterShow}}" @tap="hideFilter"></view>
        <!-- 侧边弹出搜索栏 -->
        <view class="filterBox {{filterShow?'filterAnimation':''}}" style="height:{{filterHeight}}">
          <view class="filterContent">
          <!-- 侧边栏的搜索内容 -->
          </view>
          <!-- 侧边栏底部的重置确定按钮 -->
          <view class="btnBox">
            <view @tap="resetFilter">重置</view>
            <view @tap="confirmFilter">确定</view>
          </view>
        </view>
      </swiper-item>
    </swiper>
  </view>
</template>
```

javascript片段
```javascript
<script>
    /*因为ios和android高度计算不同，android的100%就是整个高度，*/
    setFilterHeight()       {
      let s = wx.getSystemInfoSync();
      if(wx.myUtil.stricmp(s.system.split(' ')[0],'ios')){
        this.filterHeight = Math.ceil(s.windowHeight*750/s.screenWidth - 90) + 'rpx';
      } else {
        this.filterHeight = '100%';
      }
    }
</script>
```

wxss片段
```css
<style>
    /*侧边栏*/
    .filterBox {
        transition: all .5s ease;
        transform: translate3d(100%, 0, 0);
        position: absolute;
        width: 75%;
        right: 0;
        top: 0;
        bottom: 0;
        z-index: 3;
        background-color: #FFFFFF;
      }
      /*弹出动画*/
    .filterAnimation {
        transform: none;
    }
    /*遮罩层*/
    .mask {
        position: absolute;
        left: 0;
        right: 0;
        top: 0;
        bottom: 0;
        width: 100%;
        background: rgba(0, 0, 0, .5);
        z-index: 2;
    }
</style>
```

## 三、实现下图的对号选择效果
实现不难，这里主要是记录一下
![5.png](https://myblog-images1.oss-cn-beijing.aliyuncs.com/images/paidan/5.png)

wxml片段
```html
<view class="handler {{selectIdx === index ?'selected':''}}" @tap="selectHandler" data-idx="{{index}}">
    <view>{{item.name}}</view>
    <view>{{item.phone}}</view>
</view>
```

wxss片段，伪元素
```css
<style>
.selected {
    position: relative;
    border-bottom:1rpx solid @selected;
    border-top:1rpx solid @selected;
    color: @myBlue;
    background-color: #E4F1F8;
}
.selected::before,.selected::after{
    position:absolute;
    background-color:@myBlue;
    content:""
}
.selected::before{
    width:20rpx;
    height:4rpx;
    transform: rotate(35deg);
    right:90rpx;
    top:51rpx;
}
.selected::after{
    width:4rpx;
    height:34rpx;
    transform: rotate(40deg);
    right:80rpx;
    top:30rpx;
}
</style>
```
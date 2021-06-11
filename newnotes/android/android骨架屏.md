# Android骨架屏详解

# 前言

> 本文是AndroidUI加载优化系列的文章

作为客户端开发者，一定对形如菊花的loading进度条组件非常熟悉。简单来说，菊花图及菊花图衍生出来的加载动画是作为提升UI加载性能的解决方案之一，深受开发者和设计师喜爱。

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="http://cdn.yangchaofan.cn/typora/菊花图.jpg" width = "60%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图1-菊花图
      </div>
</center>



本文要介绍的骨架屏则被当今主流大厂前端视为菊花图升级版方案，用骨架屏代替菊花图的实践，愈发在Web和App开发中得到运用。

本文主要基于UI加载优化的角度回答以下问题

- 骨架屏是什么
- 骨架屏能解决什么问题
- 骨架屏实现原理
- 骨架屏的技术选型
- 骨架屏有哪些最佳实践



# 骨架屏介绍

提起骨架屏，必须要提到骨架屏（Skeleton Screen）一词的发明者——[Luke Wroblewski](https://www.lukew.com/about/) ，Google产品总监，早在2013年他首次提出了骨架屏的概念，并将这一概念运用到他的产品Polar App中，

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
src="http://cdn.yangchaofan.cn/typora/68747470733a2f2f692e6c6f6c692e6e65742f323031382f30352f30342f356165626463316237396331632e706e67" width = "60%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图2-骨架屏发明者
      </div>
</center>
Luke Wroblewski认为骨架屏应该有以下几个元素：

- 页面的空白版本，包含文本或元素的基本轮廓
- 空白版本用于传递正在渐进加载的信息

> 典型的骨架屏布局参考

## Facebook

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
src="http://cdn.yangchaofan.cn/typora/image-20210508102104630.png" width = "60%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图3-Facebook骨架屏首页实践
      </div>
</center>
## 饿了么
<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
src="http://cdn.yangchaofan.cn/typora/image-20210508102442399.png" width = "60%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图4-饿了么骨架屏首页实践
      </div>
</center>
## 京东Plus
<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
src="http://cdn.yangchaofan.cn/typora/京东plus骨架屏.gif" width = "60%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图5-京东plus骨架屏
      </div>
</center>

## 马蜂窝
<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
src="http://cdn.yangchaofan.cn/typora/af670ce6fa0b9b2a70fcbb779d10e7a3758.jpg" width = "60%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图6-马蜂窝骨架屏实践
      </div>
</center>
## 其他
<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
src="http://cdn.yangchaofan.cn/typora/68747470733a2f2f692e6c6f6c692e6e65742f323031382f30352f30342f356165626463333634313861622e706e67" width = "60%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图7-其他采用骨架屏大厂
      </div>
</center>



# 骨架屏场景

**为什么需要骨架屏？**

- 用户所需

  在最开始关于 MIT 2014 年的研究[^1]中已有提到，用户大概会在 200ms 内获取到界面的具体关注点，在数据获取或页面加载完成之前，给用户首先展现骨架屏，骨架屏的样式、布局和真实数据渲染的页面保持一致，这样用户在骨架屏中获取到关注点，并能够预知页面什么地方将要展示文字什么地方展示图片，这样也就能够将关注焦点移到感兴趣的位置。当真实数据获取后，用真实数据渲染的页面替换骨架屏，如果整个过程在 1s 以内，用户几乎感知不到数据的加载过程和最终渲染的页面替换骨架屏，而在用户的感知上，出现骨架屏那一刻数据已经获取到了，而后只是数据渐进式的渲染出来。这样用户感知页面加载更快了。

- 客户端框架的性能约束

  前端： [React](https://link.zhihu.com/?target=https%3A//github.com/facebook/react)、[Vue](https://link.zhihu.com/?target=https%3A//github.com/vuejs/vue)、[Angular](https://link.zhihu.com/?target=https%3A//github.com/angular/angular) 已经占据了主导地位，市面上大多数前端应用也都是基于这三个框架或库完成，这三个框架有一个共同的特点，都是 JS 驱动，在 JS 代码解析完成之前，页面不会展示任何内容，也就是所谓的白屏。用户是极其不喜欢看到白屏的，什么都没有展示，用户很有可能怀疑网络或者应用出了什么问题。 拿 Vue 来说，在应用启动时，Vue 会对组件中的 data 和 computed 中状态值通过 `Object.defineProperty` 方法转化成 set、get 访问属性，以便对数据变化进行监听。而这一过程都是在启动应用时完成的，这也势必导致页面启动阶段比非 JS 驱动（比如 jQuery 应用）的页面要慢一些。

  移动端：Android和IOS仍然占据统治地位，Android通常由xml->View对象->SurfaceFinger的形式将布局显示到屏幕上,IOS也类似有一个创建对象->设置数据->显示到屏幕的过程。这些过程都是在页面启动的过程完成的。如何通过降低页面启动时间，留存用户迫在眉睫。



# 骨架屏通用方案

| 名称               | 优点                                             | 缺点                                                         |
| ------------------ | ------------------------------------------------ | ------------------------------------------------------------ |
| UI 骨架屏图        | 粗暴实现，实现速度块                             | 需要UI设计师介入<br>不支持自动生成<br>需求变更维护成本大     |
| 手写骨架屏         | 为目标页定制骨架屏                               | 定制实现工作量较大<br>不支持自动生成<br/>需求变更维护成本大  |
| 自动生成静态骨架屏 | 自动为每个页面生成，自动插入到目标页面           | 脚本维护成本较大<br>生成的是静态页                           |
| 自动生成动态骨架屏 | 自动为每个页面生成骨架屏代码，自动插入到目标页面 | 不支持开发环境<br>仅支持线上URL生成骨架屏<br>存在重定向的页面，自动生成的页面效果较差<br> |



# 骨架屏最佳实践

## 饿了么前端最佳实践

饿了么前端实现骨架屏经过了2个阶段[^2]

| 实现方式                                                     | 优点               | 缺点                                                         |
| ------------------------------------------------------------ | ------------------ | ------------------------------------------------------------ |
| 手写实现，为每个页面复刻真实样式                             | 简单               | 需求变更不仅需要修改业务代码，同时也需要修改骨架屏的样式和布局；<br>机械重复的工作量较大，手写实现维护成本较大 |
| [page-skeleton-webpack-plugin](https://link.zhihu.com/?target=https%3A//www.npmjs.com/package/page-skeleton-webpack-plugin) | 自动生成，自动维护 | 饿了么自研，且需要腾出人力来维护插件                         |



## 苹果公司最佳实践

苹果公司也将骨架屏的概念写入到[iOS Human Interface Guidelines](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/LaunchImages.html) 手册中，使用了新的`launch images`概念代替了骨架屏的说法

## 马蜂窝电商前端最佳实践

基于对现有方案的借鉴，我们[^3]想到了在配置文件中指定要生成骨架屏的页面 URL 和文件输出的目录，运行时读取配置文件中的配置项，通过 Puppeteer 打开指定的页面并注入 evalDom.js 的方法。因为此 JS 是在 Puppeteer 里面执行的，所以可以获取到当前页面完整的 DOM 结构，这给我们留下了非常大的发挥空间。

最初我们是从获取到的 DOM 结构中的 body 标签出发，递归去处理页面上的所有节点，处理完成后用生成的 DIV 替换原有元素的位置。第一版方案中通过 getBoundingClientRect 和 getComputedStyle 的方法来获取元素所有计算属性和相对于视口的宽高和位置，然后结合元素本身的样式属性递归渲染，保留页面原始 DOM 嵌套层次。

但由于能够决定元素位置的属性实在太多，如 position，z-index、width、height、top、display、box-sizing、flex 等都需要考虑，导致无法聚焦对页面 DOM 结构处理的逻辑，而且这些属性在处理完成后还需要加到最终生成骨架屏节点的 style 上，这样骨架屏文件可能比原来完整的页面结构还大，这肯定不是我们希望的。

优化后的方案是用 getBoundingClientRect 和 getComputedStyle 获取元素相关属性，然后直接通过绝对定位的方式来生成最终的骨架屏节点。这样在页面上最终需要的属性主要是 position、z-index、top、left、width、height、background、border-radius。除了无法保证页面原始的 DOM 结构，其它需求基本都可以满足，也更加聚焦于节点的处理。

主要实现流程如下图：

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
src="https://oscimg.oschina.net/oscnet/1e467b28d6da470ee50d3b2d70dd575d216.jpg" width = "60%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图8-马蜂窝电商骨架屏
      </div>
</center>


# 骨架屏开源库

## Android

| 名称                                             | 用途                                                         |
| ------------------------------------------------ | ------------------------------------------------------------ |
| [Skeleton](https://github.com/ethanhua/Skeleton) | ![img](http://cdn.yangchaofan.cn/typora/13855150-1db5fa2839c21b19) |
| spruce-android                                   | ![img](http://cdn.yangchaofan.cn/typora/13855150-cdb8c851a4900d8f) |
| ShimmerRecyclerView                              | ![img](http://cdn.yangchaofan.cn/typora/13855150-c41106e6d45e5c03) |



## Web

| 名称                                                         | 用途                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [dps(draw-page-structure)](https://github.com/famanoder/dps) | ![](http://cdn.yangchaofan.cn/typora/京东plus骨架屏.gif)     |
| [page-skeleton-webpack-plugin](https://www.npmjs.com/package/page-skeleton-webpack-plugin) | ![5aebdc81eee0f](http://cdn.yangchaofan.cn/typora/68747470733a2f2f692e6c6f6c692e6e65742f323031382f30352f30342f356165626463383165656530662e706e67) |



# 参考

[^1]:[Akamai](http://www.akamai.com/html/about/press/releases/2009/press_091409.html) 
[^2]:[一种自动化生成骨架屏的方案/jocs.github.io · GitHub](https://github.com/Jocs/jocs.github.io/issues/22)
[^3]:[一种对开发更友好的前端骨架屏自动生成方案 - 马蜂窝技术](https://www.cnblogs.com/mfwtech/p/11474796.html)
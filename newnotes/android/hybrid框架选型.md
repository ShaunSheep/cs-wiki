## Hybrid框架选型

|                    | [VasSonic](https://github.com/Tencent/VasSonic) | **[ DSBridge-Android](https://github.com/wendux/DSBridge-Android)** | [AgentWeb](https://github.com/Justson/AgentWeb)                                       | [JsBridge](https://github.com/lzyzsd/JsBridge) | **[WebViewJavascriptBridge](https://github.com/marcuswestin/WebViewJavascriptBridge)** | [cordova-android](https://github.com/apache/cordova-android) | [Android-AdvancedWebView](https://github.com/delight-im/Android-AdvancedWebView) | [ByWebView](https://github.com/youlookwhat/ByWebView) |
| ------------------ | -------------------------------------------- | ---------------------------------------------------------------------- | ---------------------------------------------------------------------------------- | ------------------------------------------- | ----------------------------------------------------------------------------------------- | --------------------------------------------------------- | ----------------------------------------------------------------------------- | -------------------------------------------------- |
| 介绍               |                                              |                                                                        | [AgentWeb ， 一个简洁易用的 Android Web 库 )](https://www.jianshu.com/p/c80da1c41af7) |                                             |                                                                                           |                                                           |                                                                               |                                                    |
| 特点               | 专注首屏速度                                 |                                                                        |                                                                                    |                                             |                                                                                           |                                                           |                                                                               |                                                    |
| 支持AndroidAPI版本 |                                              |                                                                        |                                                                                    |                                             |                                                                                           | 14 - 23                                                   |                                                                               |                                                    |
| 开发者             | 腾讯                                         | [wendux](https://github.com/wendux)                                       | [Justson](https://github.com/Justson)                                                 | [lzyzsd](https://github.com/lzyzsd)            | [marcuswestin](https://github.com/marcuswestin)                                              |                                                           | [delight-im](https://github.com/delight-im)                                      | [youlookwhat](https://github.com/youlookwhat)         |
| 上一次更新时间     | 2019年                                       | 2018年                                                                 |                                                                                    | 2021年3月                                   | 2017年                                                                                    | 2021年5月                                                 | 2021年                                                                        | 2020年                                             |
| 创建时间           | 2017年                                       | 2016年                                                                 | 2019年                                                                             | 2015年                                      | 2013年                                                                                    | 2015年                                                    | 2016年                                                                        | 2015年                                             |

## Hybrid其他辅助能力

- [X5webview](https://x5.tencent.com/docs/index.html)
  - 兼容好：无系统内核的碎片化问题，更少的兼容性问题,支持AndroidAPI版本1-30
  - 不提供Native通信，只提供浏览器能力
  - 体验优：支持夜间模式、适屏排版、字体设置等浏览增强功能；
  - 功能全：在Html5、ES6上有更完整支持；
  - 视频和文件格式的支持x5内核多于系统内核
- [cordova-plugin-crosswalk-webview](https://github.com/crosswalk-project/cordova-plugin-crosswalk-webview)
  - 兼容性最好
  - 3年没维护了


| 方案            | 方案说明                                           | 实际效果 | 优缺点                                                          | 缺点                                                               | html5test分数 |
| --------------- | -------------------------------------------------- | -------- | --------------------------------------------------------------- | ------------------------------------------------------------------ | ------------- |
| 系统自带WebView | Android默认                                        | 最差     | 优：没有额外的JAR及负担，原生API                                | 兼容性，性能在不同手机上显示差别很大                               | 最差          |
| X5 WebView      | 腾讯产品，微信，QQ浏览器就是使用X5内核             | 一般     | 优：提供了一个兼容性的解决方案,且微信，QQ浏览器都在用，可信度高 | 解决的能力一般，而且某些方面反而加大了开发工作量;而且不支持cordova | 一般          |
| crosswalk       | 国外为Android提供的一个融合chrome webkit的解决方案 | 最佳     | 优:没有兼容性，性能问题,且支持corodva                           | 18M的包，而且区分不同的arm,x86等CPU                                | 较佳          |

## Hybrid常见问题

- **1px 问题**
- **响应式布局**
- **iOS 滑动不流畅**
- **iOS 上拉边界下拉出现白色空白**
- **页面件放大或缩小不确定性行为**
- **click 点击穿透与延迟**
- **软键盘弹出将页面顶起来、收起未回落问题**
- **iPhone X 底部栏适配问题**
- **保存页面为图片和二维码问题和解决方案**
- **微信公众号 H5 分享问题**
- **H5 调用 SDK 相关问题及解决方案**
- **H5 调试相关方案与策略**

## 参考

1. [Hybrid APP架构设计思路](https://segmentfault.com/a/1190000004263182)
2. [吃透移动端 H5 与 Hybrid｜实践踩坑12种问题汇总 (juejin.cn)](https://juejin.cn/post/6844904024790007815)
3. [android webView 内核对比](https://blog.csdn.net/w2ndong/article/details/94622399)

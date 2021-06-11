# Android组件

以下示例代码都在[**awesome-android-code**](https://github.com/ShaunSheep/awesome-android-code/tree/master/android-component)

## 自定义组件

| 名称                                              | 用途                                 | 地址                                                         |
| ------------------------------------------------- | ------------------------------------ | ------------------------------------------------------------ |
| 流式布局                                          | 热榜标签、搜索推荐标签、个性标签设置 | [custom-flowlayout](https://github.com/ShaunSheep/awesome-android-code/blob/master/android-component/custom-ui/custom-flowlayout/flowlayout.md) |
| 日历                                              | 课堂签到、便签笔记、闹钟提醒         | [custom-calendar](https://github.com/ShaunSheep/awesome-android-code/tree/master/android-component/custom-ui/custom-calendar) |
| 绘画板                                            | 涂鸦、签名、笔记、便签               | [ScaleSketchPadDemo](https://github.com/ShaunSheep/ScaleSketchPadDemo) |
| [多点触控](https://github.com/mry1/CustomView  )  |                                      |                                                              |
| [刮刮卡](https://github.com/tt88050643/GuaGuaKa ) |                                      |                                                              |



## 基础组件

| 名称                                                         | 用途                                                    | SampleCode                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ |
| Viewpager                                                    | Tab类布局、Gallery图库、Banner轮播图                    | [地址](https://github.com/ShaunSheep/awesome-android-code/blob/master/android-component/viewpager/viewpager.md) |
| [ViewPager2](https://developer.android.google.cn/jetpack/androidx/releases/viewpager2) | 同上                                                    |                                                              |
| [paging *](https://developer.android.google.cn/jetpack/androidx/releases/paging) | 在页面中加载数据，并在 RecyclerView 中呈现。            |                                                              |
| Fragment                                                     | 碎片                                                    |                                                              |
| Activity                                                     | 界面                                                    |                                                              |
| Service                                                      | 服务                                                    |                                                              |
| BroadcastReceiver                                            | 广播                                                    |                                                              |
| Contentprovider                                              | 内容提供                                                |                                                              |
| [contentpager](https://developer.android.google.cn/jetpack/androidx/releases/contentpager) | 在后台线程中加载 ContentProvider 数据并进行分页         |                                                              |
| [concurrent](https://developer.android.google.cn/jetpack/androidx/releases/concurrent) | 使用协程将任务移出主线程，并充分利用 ListenableFuture。 |                                                              |
| [camera *](https://developer.android.google.cn/jetpack/androidx/releases/camera) | 构建移动相机应用。                                      |                                                              |
| [media](https://developer.android.google.cn/jetpack/androidx/releases/media) | 与其他应用共享媒体内容和控件。已被 media2 取代。        |                                                              |
| [media2](https://developer.android.google.cn/jetpack/androidx/releases/media2) | 与其他应用共享媒体内容和控件。                          |                                                              |
| AIDL                                                         | IPC                                                     |                                                              |

## 视图组件[^1]



| 名称                                                         | 用途                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Material Design 组件*](https://material.io/develop/android) | 适用于 Android 的模块化、可自定义 Material Design 界面组件。 |
| [asynclayoutinflater](https://developer.android.google.cn/jetpack/androidx/releases/asynclayoutinflater) | 异步膨胀布局以避免界面出现卡顿。                             |
| View                                                         | 自定义                                                       |
| ViewGroup                                                    | 自定义                                                       |
| ConstraintLayout                                             |                                                              |
| LinearLayout                                                 |                                                              |
| MotionLayout                                                 | ConstraintLayout的子类，构件运动和组件动画                   |
| ListView                                                     |                                                              |
| [recyclerview](https://developer.android.google.cn/jetpack/androidx/releases/recyclerview) | 列表布局                                                     |
| [cardview](https://developer.android.google.cn/jetpack/androidx/releases/cardview) | 卡片布局                                                     |
| SurfaceView                                                  |                                                              |
| LayoutParams                                                 | 布局参数                                                     |
| AdapterView                                                  |                                                              |
| Adapter                                                      | ListView所需适配器                                           |
| ArrayAdapter                                                 | Adapter子类                                                  |
| SimpleCursorAdapter                                          | Adapter子类                                                  |
| [cursoradapter](https://developer.android.google.cn/jetpack/androidx/releases/cursoradapter) | 向 ListView 微件提供光标数据。                               |
|                                                              |                                                              |
| [transition](https://developer.android.google.cn/jetpack/androidx/releases/transition) | 使用开始和结束布局为界面中的动作添加动画效果。               |
| [drawerlayout](https://developer.android.google.cn/jetpack/androidx/releases/drawerlayout) | 实现 Material Design 抽屉式导航栏微件。                      |
| [slidingpanelayout](https://developer.android.google.cn/jetpack/androidx/releases/slidingpanelayout) | 实现滑动窗格界面模式。                                       |
| [coordinatorlayout](https://developer.android.google.cn/jetpack/androidx/releases/coordinatorlayout) | 定位顶层应用微件，例如 AppBarLayout 和 FloatingActionButton。 |
| [customview](https://developer.android.google.cn/jetpack/androidx/releases/customview) | 实现自定义视图。                                             |
| [gridlayout](https://developer.android.google.cn/jetpack/androidx/releases/gridlayout) |                                                              |
| [swiperefreshlayout](https://developer.android.google.cn/jetpack/androidx/releases/swiperefreshlayout) | 实现下拉刷新的界面模式。                                     |
| [textclassifier](https://developer.android.google.cn/jetpack/androidx/releases/textclassifier) | 识别文本中的对话、链接、选定内容和其他类似构造内容。         |
| [transition](https://developer.android.google.cn/jetpack/androidx/releases/transition) | 使用开始和结束布局为界面中的动作添加动画效果。               |
| [vectordrawable](https://developer.android.google.cn/jetpack/androidx/releases/vectordrawable) | 渲染矢量图形。                                               |
| [dynamicanimation](https://developer.android.google.cn/jetpack/androidx/releases/dynamicanimation) | 使用基于物理特性的动画 API 制作流畅的动画。                  |
| [interpolator](https://developer.android.google.cn/jetpack/androidx/releases/interpolator) | 在旧版平台上使用动画插值器。                                 |
| [palette](https://developer.android.google.cn/jetpack/androidx/releases/palette) | 从图片中提取具有代表性的调色板。                             |
|                                                              |                                                              |

## Jetpack组件[^2]

| 名称                                                         | 用途                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [webkit](https://developer.android.google.cn/jetpack/androidx/releases/webkit) | 在 Android 5 及更高版本上使用新式 WebView API。              |
| [databinding *](https://developer.android.google.cn/jetpack/androidx/releases/databinding) | 使用声明性格式将布局中的界面组件绑定到应用中的数据源。       |
| [navigation *](https://developer.android.google.cn/jetpack/androidx/releases/navigation) | 构建和组织应用内界面，处理深层链接以及在屏幕之间导航。       |
| [startup](https://developer.android.google.cn/jetpack/androidx/releases/startup) | 实现一种在应用启动时初始化组件的简单、高效方法。             |
| [security](https://developer.android.google.cn/jetpack/androidx/releases/security) | 安全地管理密钥并对文件和 sharedpreferences 进行加密。        |
| [savedstate](https://developer.android.google.cn/jetpack/androidx/releases/savedstate) | 编写可插入组件，这些组件会在进程终止时保存界面状态，并在进程重启时恢复界面状态。 |
| [collection](https://developer.android.google.cn/jetpack/androidx/releases/collection) |                                                              |
| [work *](https://developer.android.google.cn/jetpack/androidx/releases/work) | 调度和执行可延期且基于约束条件的后台任务                     |
| [datastore](https://developer.android.google.cn/jetpack/androidx/releases/datastore) | 以异步、一致的事务方式存储数据，克服了 SharedPreferences 的一些缺点 |
| [lifecycle *](https://developer.android.google.cn/jetpack/androidx/releases/lifecycle) | 构建生命周期感知型组件，这些组件可以根据 Activity 或 Fragment 的当前生命周期状态调整行为。 |
| [loader](https://developer.android.google.cn/jetpack/androidx/releases/loader) | 加载配置更改后继续存在的界面数据。                           |

## Framework核心组件[^3]

| 名称                   | 用途                  | 图解                                                         | 参考                                                         |
| ---------------------- | --------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ActivityManagerService | 组件管理服务          | <img style="width:55%" src="http://cdn.yangchaofan.cn/BlogGifRes/20210320/V5XaAkCl7siV.png"> | [ActivityManagerService概述](https://blog.csdn.net/fu_kevin0606/article/details/79748015)<br>[Android应用程序的Activity启动过程简要介绍和学习计划](https://blog.csdn.net/Luoshengyang/article/details/6685853?) |
| WindowManagerService   | 窗口进行管理          | <img style="width:55%" src="http://cdn.yangchaofan.cn/BlogGifRes/20210320/RHDBPeRVtSxS.png"> |                                                              |
| PowerManagerService    | 电源管理服务          | <img style="width:100%" src="http://cdn.yangchaofan.cn/BlogGifRes/20210320/XqDsXowltW1n.png"> | [PowerManagerService框架解析](https://www.jianshu.com/p/dedb97c5dd05) |
| PackageManagerService  | 程序包管理服务        | <img style="width:80%" src="https://img-blog.csdn.net/20131022154631359?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbmV3X2FiYw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"> | [PackageManagerService概述](https://blog.csdn.net/new_abc/article/details/12949179) |
| AudioService           | 音频管理服务          | <img style="width:55%" src="http://cdn.yangchaofan.cn/BlogGifRes/20210320/R6dRaSKgi0X5.png"> |                                                              |
| Graphics               | 图形管理              | <img style="width:55%" src="http://cdn.yangchaofan.cn/BlogGifRes/20210320/XDG3fBgXiA2d.png"> |                                                              |
| Surfaceflinger         | Layer合成（composer） | <img style="width:55%;background-color:white" src="http://cdn.yangchaofan.cn/BlogGifRes/20210320/XGgKWOg4W4dU.png"> | [SurfaceFlinger](https://blog.csdn.net/ckanhw/article/details/104289535) |
| Input系统              |                       | <img style="width:55%" src="http://cdn.yangchaofan.cn/BlogGifRes/20210320/hJTH4zwETGQ3.png"> |                                                              |



## 

# ViewPager



| 问题                                                         | 解决思路                                                     | 效果评价 |
| ------------------------------------------------------------ | ------------------------------------------------------------ | -------- |
| ViewPager[刷新问题详解](https://blog.csdn.net/axi295309066/article/details/53574976) | FragmentPagerAdpater#instantiateItem源码原理，Do we already have this fragment?<br>原来他会先去FragmentManager里面去查找有没有相关的fragment:<br>如果有就直接使用<br>如果没有才会触发fragmentpageadapter的getItem方法获取一个fragment。<br>所以你更新的fragmentList集合是没有作用的，还要清除FragmentManager里面缓存的fragment。<br>解决办法：在继承的fragmentpageadapter类里面添加这么一个方法 | 9分      |
| [Android FragmentPagerAdapter数据刷新notifyDataSetChanged没效果研究](http://blog.sina.com.cn/s/blog_783ede03010173b4.html) | adapter更新数据前，先使用FragmentManager强制清空缓存<br>原创思路 | 10分     |
| [ViewPager刷新缓存问题 FragmentPagerAdapter+FragementStatePagerAdapter](ViewPager刷新缓存问题 FragmentPagerAdapter+FragementStatePagerAdapter) | 清楚的讲解了每个方法的坑                                     | 5分      |


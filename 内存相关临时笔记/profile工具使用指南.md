profile工具使用指南

参考

[使用内存分析器查看应用的内存使用情况  | Android 开发者  | Android Developers (google.cn)](https://developer.android.google.cn/studio/profile/memory-profiler?hl=zh#profile-memory-techniques)





# Profile实时预览

MemoryProfile工具栏自顶向下分为三个区域

1. 工具栏
2. 时间轴区，包括页面事件轴、每个类内存占用轴、时间轴、对象数轴、垃圾回收事件轴
3. 内存分配详情区

![profile工具指南实时预览图](.\pics\profile工具指南实时预览图.jpg)

![profile工具指南实时预览图-可视化展示类的占比](.\pics\profile工具指南实时预览图-可视化展示类的占比.jpg)

下面详细介绍一下每个区域每个内容的含义和用途。



## ②堆转储按按钮

用于捕获点击按钮之后一段时间内的内存分配情况。捕获完会自动进入**堆转储预览界面**

## ③ Allocation Tracking区

含义：设置采样周期。

- Full实时监控每一个对象的分配情况
- Sample定期监控对象的分配情况
- Off停止跟踪对象内存分配情况。

用途：

1. 提高性能剖析的性能，避免应用卡顿
2. 自定义Allocation Tracking类，可以删去无用的类的内存分配情况，只关注应用包内部的类内存分配情况

## ⑥手势事件轴

含义：一个能显示屏幕旋转事件、用户手势事件、手势耗时等信息的区域。

用途：判断某个事件后，该事件之前产生的对象是否被回收，如屏幕旋转了，旋转之前创建的对象是应该被回收的；

## ⑦页面生命周期状态轴

含义：一个显示Activity/Fragment的生命周期状态改变信息的区域，如Stopped、destroyed、resumed等状态变化，都会体现到该区域上

用途：判断一个页面结束后，该页面创建出的对象是否被回收，如果未被回收则表明出现了内存泄漏情况。

## ⑧内存使用量层叠轴

含义：一个展示内存使用量的区域

- 显示每个类型使用的内存量

- 白色虚线显示对象分配的数量

- 垃圾回收事件的图标

用途：

- 查看内存分配数值内存数值展示的内容有限，一般不看此处。
- 查看内存增长或下降的情况，锯齿状表明频繁的创建和释放；只增长不释放表明可能存在泄漏；突然增长过大，表明可能OOM；
- 在一个页面进入和退出前后的时间点，内存非常接近，表明此处内存使用很合理，没有内存泄漏。

# 堆转储预览

![hprof分析-hprof文件预览功能](http://cdn.yangchaofan.cn/typora/hprof分析-hprof文件预览功能.jpg)

## 如何生成Hprof

- 通过调用`Debug.dumpHprofData(String filePath)`方法来生成hprof文件,可以在App任意位置调用此代码，通常是页面退出或者App退出
- 通过执行shell命令`adb shell am dumpheap pid /data/local/tmp/x.hprof`来生成指定进程的hprof文件到目标目录
- Profile点击堆转储按钮

## **①Heap分区**

1. `app heap`：当前`App`在堆中占据的内存
2. `image heap`：系统启动镜像，包括启动期间预加载的类
3. `zygote heap`：所有`App`的父进程，所有`App`共享`zygote`的内存空间，`zygote`预加载了许多资源和代码，供所有`App`读取
4. `JNI heap`：显示 Java 原生接口 (JNI) 引用被分配和释放到什么位置的堆。

## ②实例类型分组展示区

- `Arrange by class`：根据类名称对所有分配进行分组。
- `Arrange by package`：根据软件包名称对所有分配进行分组。
- `Arrange by callstack`：将所有分配分组到其对应的调用堆栈。

## **⑩Instance View**

1. `depth`：从`gc root`到当前对象的引用链最短深度。被gc root引用到的对象不会被回收。

   个人收获：`depath = 0`，表示不被gc root引用，即会被垃圾回收器回收。

   个人收获：常见`gc root`有5种——局部变量、`Activity threads`、静态变量、`JNI`引用、类加载器

2. `native size`：`native`对象占据的内存大小

3. `shallow size`：对象本身占用的内存，何为对象本身？不包括它引用的其他实例

   个人收获：`shallow size = 类定义+ 父类变量所占空间大小 + 类成员变量所占空间 + [ alignment]`

   类定义：固定为8`Byte`

   父类变量所占空间大小：当前类继承了其他类的成员变量，显然这些变量是占用空间的

   自身变量：当前类的成员变量，如果是基本数据类型，则按基本类型计算；如果是引用数据类型，则固定为4byte，当前类仅持有变量名，

   `alignment`：指位数对齐，目的是让`shallow zie`的值为8的倍数，如某个类A，前三项计算结果未15`byte`，则`shallo size`为了达到8的倍数，会设置一个`alignment`值，凑够16`byte`。

4. `retained size`：Retained Size是指, 当实例A被回收时, 可以同时被回收的实例的Shallow Size之和



> Instance View易混淆概念：

`shallow size`：`Shallow Size`是指实例**自身**占用的内存, 可以理解为保存该'数据结构'需要多少内存, 注意**不包括**它引用的其他实例

`retained size`：实例A的`Retained Size`是指, 当实例A被回收时, 可以**同时被回收**的实例的Shallow Size之和

![img](https://upload-images.jianshu.io/upload_images/1359811-2a89b5bec9e345ab.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

图中A, B, C, D四个实例, 为了方便计算, 我们假设所有实例的Shallow Size都是1kb

删除D实例：垃圾回收期会移除D实例;`D实例的Retained Size=Shallow Size=1kb`

删除C实例：垃圾回收器会移除C和D实例;`C实例的Retained Size = C实例的Shallow Size + D实例的Shallow Size = 2kb`

删除B实例：垃圾回收器会移除B实例，因为C仍然被A引用，所以C不会被移除，同理D也不会被移除;`B实例的Retained Size=Shallow Size=1kb`

删除A实例：垃圾回收器会移除B、C、D实例;`A实例的Retained Size=4kb`







工具设置

android27以下需要特殊设置



android27以上无需特殊设置

工具界面

事件轴

时间轴还是什么轴的

各个按钮功能



术语

抖动

泄漏

oom



片段式的经验

native区域需要关注吗？

需要，bitmap = null 是不够的，还需要recycle，才能释放native区域的内存
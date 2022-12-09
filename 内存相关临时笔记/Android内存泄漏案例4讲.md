# Android内存泄漏案例5讲

内存泄漏的关注点——对象占据的内存

- 本该回收的对象，无法被垃圾处理器回收；
- 程序不再使用的对象，无法被垃圾处理器回收；
- 在页面生命周期销毁后，页面持有、创建过的对象无法被垃圾处理器回收；
- 在任务（线程、Service、广播）结束后，任务持有、创建过的对象无法被垃圾处理器回收

Android内存问题l类型概述

内存抖动的表现形式：

1. 忽高忽低，锯齿状

内存泄漏的表现形式：

1. 页面或任务已经结束，相关的对象仍然被GC root索引，导致垃圾回收器无法回收，内存片段仍然存在

内存溢出的表现形式：

1. App可用内存已到达系统规定的上限，如某型号手机规定单个App最大可分配内存为196mb

2. 系统能为App分配的内存不足，系统可分配内存已经到达上限，如系统可用只有1mb，app需要20mb

   

## 内存泄漏的危害

创建8000个实例就内存溢出了

```java
package org.example.a;

import java.util.ArrayList;
import java.util.List;

class Outer{
    private int[] data;

    public Outer(int size) {
        this.data = new int[size];
    }

    class Innner{

    }

    Innner createInner() {
        return new Innner();
    }
}

public class Demo {
    public static void main(String[] args) {
        List<Object> list = new ArrayList<>();
        int counter = 0;
        while (true) {
            list.add(new Outer(100000).createInner());
            System.out.println(counter++);
        }
    }
}
```



## Android中内存泄漏的判断方式

> 判断一个对象是否无法被回收，最佳的判断方式是定义清楚这个对象预期的生命周期，通常对象是随着页面、任务的生命周期产生和消亡的，Android中可以借此机制判断是否存在泄漏现象：

1. `Activity`:通过查看`Activity#mDestroyed`属性来判断`Activity`是否已经销毁，如果为true，表明该`Activity`已经被标记为销毁状态，此时**hprof文件中若仍然存在此Activity**，则表明这个`Activity`占据的内存处于泄漏状态；
2. `Fragment`：通过`Fragment#mFragmentManager`属性来判断该`Fragment`是否处于无用状态，如果`mFragmentManager`为空，且**hprof文件中若仍然存在**此`Fragment`，则表明`Fragment`占据的内存处于泄漏状态；
3. `View`：通过`un wrapper#mContext`获得`Activity`，如果`Activity`不为空，则按照判断`Activity`的方式判断`Activity`是否泄漏
4. `ContextWrapper`：通过`unwrapper ContextWrapper获`得`Activity`，如果`Activity`不为空，则按照判断Activity的方式判断Activity是否泄漏
5. `Dialog`，通过判断`mDecor`是否为空。`mDecor`为空，表明`Dialog`处于不被引用的状态，`mDecor`仍然**存在在hporf里**且为空，则表明Dialog泄漏了
6. `MessageQueue`：通过判断`MessageQueue#mQuitting`或`mQuiting`是否退出，如果该值是`true`且未被回收，则认为是泄漏。
7. `ViewRootImpl`：通过`ViewRootImpl`是否为空来判断，为空表明处于无用状态，为空且未被回收则被认为是泄漏
8. `Window`：通过`Window#mDestroyed`来判断`window`是否处于无用状态，`mDestroyed`为`true`且未被回收，则认为是泄漏
9. `Toast`：拿到`mTN`，通过`mTN#mView`是否为空来判断当前`Toast`是否已`经hide`，如果`mView`为空，表明`Toast`已经`hide`，此时`Toast`未被回收则认为是泄漏
10. `Editor`：`Editor`是用于TextView处理`editable text`的辅助类，通过`ditor#mTextView`为空来判断`Ediator`是否处于无用状态；如果mTextView为空且未被回收则认为`Editor`泄漏了



内存泄漏的典型 场景

1. 非静态内部类、非静态匿名内部类持有外部类对象的引用：常见的如`Listener`、`CallBack`、`Handler`、`Dialog`
2. 非静态的`Handler`，持有`Activity`，`Message`持有`Handler`，`Message`被`MessageQueue`持有，主线程`MessageQueue`持久存在，导致Activity不会被释放
3. 资源对象未关闭：数据库连接、`Cursor`、`IO`流使用完后未close
4. 属性动画：未及时使用`cancel`关闭；`Animator`持续存在，导致`Animator`持有的`Activity`、`Fragment`、`View`泄漏（`Animator#updateListener`一般都是匿名内部类，匿名内部类的问题参考场景1）
5. 逻辑问题：广播监听后未及时解注册；



## 









## 案例1——hprof文件显示出了内存泄漏

本案例如图所示，Profiler将内存泄漏的分析结果直接显示到了工具栏上，我们只需要按步骤展开方法调用链即可找到泄漏点

![hprof文件分析内存泄漏——有leaks的](http://cdn.yangchaofan.cn/typora/hprof文件分析内存泄漏——有leaks的.jpg)

①点+导入`hprof`文件

②选择`app` 堆存储

③根据类名排序

④仅展示`Activity`、`Fragment`内存泄漏

⑤过滤关键字，如`app`进程的包名

⑥点击`Leaks`，下方`Class Name`区域会展示出内存泄漏的类名

⑦在`Class Name`区域点击选择要观察的类名

⑧在`Instance List`区域点击选择该类的实例

⑨在`Instace Details`区域点击`References` 选项卡

⑩选中 `GC root only`，一层一层展开引用链

也可以在第9步时候观察Activity的生命周期来判断内存泄漏

可以看到一个页面存在三个实例，这三个实例都因为某些原因无法被垃圾回收器回收。我们逐一分析下。

**第三个Activity实例:**

![hprof-观察Activity生命周期判断是否泄漏](http://cdn.yangchaofan.cn/typora/hprof-观察Activity生命周期判断是否泄漏.jpg)

首先点击`Fields`，查看`Activity`的生命周期，可以看到`Instacen Details - Fields -Instance视图`中`Activity#mDestroyed = true` ，表明此页面已经销毁了

> Android中内存泄漏的判断方式#Activity内存泄漏判断方式一节也提到观察mDestroyed字段来判断Activity是否出现了内存泄漏

![image-20221114174703825](http://cdn.yangchaofan.cn/typora/image-20221114174703825.png)

其次点击`References`，点一层层展开引用链，可以看到`Activity`使用了`PlayUtil`，`PlayUtil`初始化的时候传入了此`Activity`，而`PlayUtil`是一个单例类，该单例类全局持有了此`Activity`的实例，导致`Activity`一直被`PlayUtil`持有着，在`Activity`生命周期结束后，`Activity`占据的堆内存，无法被垃圾回收器回收，出现了泄漏的情况。

单例类持有`Activity`,导致`Activity`占据的内存无法被释放，遇到这种问题，解决起来也很容易：

1. 替换`context`，使用`ApplicationContext`
2. 使用一个更轻量无任何业务的`Activity`来初始化`PlayUtil`
3. 如无必要不要使用`context`

`PlayUtil`问题，笔者采用了方法3：

修改前

```java
  private PlayUtil(Context context) {
    m_MediaPlayerIml = MediaPlayerIml.getInstance();
     this.m_context = context;
   }
```

修改后

```java
  private PlayUtil(Context context) {
    m_MediaPlayerIml = MediaPlayerIml.getInstance();
     // 兼容老代码，保留入参context，但不使用，避免内存泄漏
//     this.m_context = context;
  }
```

这样可以达到`PlayUtil`不持有`Activity`的目的，在`Activity#onDestroyed`时候，`Activity`占据的内存将被垃圾回收器回收。



**接着我们继续看该Activity的第二个实例**，查看该实例的生命周期可知该Activity已经处于Destroyed状态，但内存未被回收，这是为什么呢？

![hprof分析-dialog持有Activity导致泄漏](http://cdn.yangchaofan.cn/typora/hprof分析-dialog持有Activity导致泄漏.jpg)

我们继续查看`References`，可以看到Activity被`LoadProgress`持有了，且无法被释放。



笔者分析可能是`Activity#showLoading`期间，`Activity`转入后台或其他因素，并未来得及调用`dissLoading`导致`LoadProgress`工具类持续持有该Activity的引用，产生了内存泄漏。我们来看看问题代码

**问题代码**

Activity#initLoading，传入了当前Activity

```
  private void initLoading() {
        if (this.viewModel != null) {
            this.viewModel.showLoading.observe(this, new Observer<Boolean>() {
                public void onChanged(Boolean aBoolean) {
                    if (aBoolean) {
                        if (!LoadProgress.get().getDialog(BaseActivity.this).isShowing()) {
                            LoadProgress.get().getDialog(BaseActivity.this).show();
                        }
                    } else {
                        LoadProgress.get().dismissDialog(BaseActivity.this);
                    }

                }
            });
        }
    }

```

LoadProgress内使用HashMap缓存了传入的Activity

```
  this.dialogs.put(context, lp);
```

**解决方法**

遇到这种问题也很好处理——在适当的时机清除`HashMap`缓存的`Activity`引用。

按照这种思路，我们看到：`LoadProgress`工具类里提供了`HashMap`的清空方法`LoadProgress#cleanUpTrash`，那么我们在合适的地方，清空`Activity缓存即可

在`Activity#onDestroyed`调用

```
    @Override
    protected void onDestroy() {
        super.onDestroy();
        // 清空缓存
        LoadProgress.get().cleanUpTrash();
    }
```

这样，当`Activity`生命周期处于`onDestroyed`的时候，`HashMap`会清空持有的`Activity`，避免`Activity`内存泄漏

**最后我们来看**`Activity`**的第一个实例**

有前两个泄漏点的分析经验，可得知`Activity`此时已经处于`onDestroyed`状态，被某个类引用了，导致`Activity的`内存无法回收.

![hprof分析-MediaplayerListener未解绑](http://cdn.yangchaofan.cn/typora/hprof分析-MediaplayerListener未解绑.jpg)

熟能生巧，我们按图索骥可看到是`MediaplayerIml`通过`mMediaPlayListenerCacheList`持有了该Activity，该类是用于存储播放回调的，用于`AIDL`服务端通知`Client`的回调，更新播放界面。

**问题代码**

`Activity#onCreate`时候调用了注册回调

```java
mediaPlayerIml.registerListener(playListener);
```

`MediaPlayerIml#registerListener`内部通过`list`缓存了`Activity`

```java
    private List<MediaPlayListener> mMediaPlayListenerCacheList = new ArrayList<>();

 public synchronized void registerListener(MediaPlayListener listener) {
        if (!mMediaPlayListenerCacheList.contains(listener)) {
            mMediaPlayListenerCacheList.add(listener);
        }
    }
```

**解决方法**

在适当的时机清除`MediaPlayerIml`持有的`Activity`:

`Activity#onDestroy`是个很不错的选择

```java
    @Override
    protected void onDestroy() {
        super.onDestroy();
        if(mediaPlayerIml!=null){
            mediaPlayerIml.unregisterListener(playListener);
        }
        // 清空缓存
        LoadProgress.get().cleanUpTrash();
    }
```

**总结3个内存泄漏点**

1. `PlayUtil`构造函数持有`Activity`未释放
2. `LoadProgress`成员变量`HashMap`持有`Activity`未释放
3. `AIDL`远程服务端持有`Listener`，`Listener`持有`Activity`未释放

**优化效果**

优化前`Activity`进入3次创建了3份实例：

优化前，多次进入该页面，产生4个实例，有3个实例无法被垃圾回收，出现了内存泄漏的情况

![hprof分析-专辑列表页优化前](http://cdn.yangchaofan.cn/typora/hprof分析-专辑列表页优化前.jpg)

优化后，多次进入该页面，只产生一个实例

![hprof分析-专辑列表页优化后](http://cdn.yangchaofan.cn/typora/hprof分析-专辑列表页优化后.jpg)

根据`Retained Szie`来看，优化前后节省内存，效果减少至原先的 （49937/332 = ）$1/150$ 。由此可见`Activity`及时回收，极大的节省了内存占用。



## 案例2——hprof文件显示出Fragment内存泄漏

接下来我们来看fragment内存泄漏，老规矩查看fields和references，确保它符合内存泄漏的情形；我们点击jump to source查看泄漏的位置

![hprof分析-fragment内存泄漏](http://cdn.yangchaofan.cn/typora/hprof分析-fragment内存泄漏.jpg)

问题代码：`Fragment#MZBannerView#内部类Runnbale`

```java
/**
 * Banner 切换时间间隔
 */
private int mDelayedTime = 5000;
private final Runnable mLoopRunnable = new Runnable() {
        @Override
        public void run() {
            if (mIsAutoPlay) {
                mCurrentItem = mViewPager.getCurrentItem();
                mCurrentItem++;
                if (mCurrentItem > mAdapter.getCount() - 1) {
                    mCurrentItem = 0;
                    mViewPager.setCurrentItem(mCurrentItem, false);
                    mHandler.postDelayed(this, mDelayedTime);
                } else {
                    mViewPager.setCurrentItem(mCurrentItem);
                    mHandler.postDelayed(this, mDelayedTime);
                }
            } else {
                mHandler.postDelayed(this, mDelayedTime);
            }
        }
    };
```

可以看到`Fragment`内存泄漏的第一个原因，内部类`runnable`持有了`view`的实例，每个`Runnbale`会发送一个延时5秒的消息，消息发送期间，有可能`view`、`fragment`已经结束了生命周期，此时产生了内存泄漏。

解决办法也很简单，view离开窗口的时候，释放`Handler`中消息，释放`Runnbale`对`view` 的引用。

1. 静态`Runnbale`内部类+对`view`的弱引用(此部分代码与前面的示例很相似，不重复贴代码了)

2. 离开窗口`remove#handler`消息

`view`对`Activity`暴露了`pause`方法，在`Activity`销毁时，强制清`空handler`的任务；

```java
    /**
     * 停止轮播
     */
    public void pause() {
        mIsAutoPlay = false;
        mHandler.removeCallbacks(mLoopRunnable);
        mBannerPageClickListener = null;
    }

```

Activity代码：

```java
    @Override
    public void onDestroy() {
        super.onDestroy();
        dataBing.homeBanner.pause();
    }

```





## 案例3——view内存泄漏

前文提到，`profile#Leaks`视图无法展示非`Activity`、非`Fragment`的内存泄漏，换言之，除了`Activity`、`Fragment`的内存泄漏外，其他类的内存问题我们只能自己检索`hprof`文件查询了。

下面有一个极佳的view内存泄漏例子，它的操作步骤为：

1. 播放音乐，唤醒音乐悬浮窗

2. 播放一段时间后，关闭音乐悬浮窗

3. 重复步骤1和2

悬浮窗长这个样

<img src="http://cdn.yangchaofan.cn/typora/右侧悬浮窗.jpg" alt="右侧悬浮窗" style="zoom:50%;" />

我们重复三次之后，得到一份hprof文件，下面我们来分析一下内存泄漏问题

![hprof分析-view内存泄漏-优化前](http://cdn.yangchaofan.cn/typora/hprof分析-view内存泄漏-优化前.jpg)

①输入`view`的名称

②选择`view`

③可以看到分配了3个实例对象

④`Instance List `视图显示，`view`有3个实例对象及其引用

我们从上至下依次看3分实例的调用链

**view的第一个实例**

先查看Fields区域，观察`mLayoutmode`值，判断`view`是否离开了窗口，如果已经离开了窗口，表明view未被回收，存在内存泄漏

![image-20221116103835001](http://cdn.yangchaofan.cn/typora/image-20221116103835001.png)

可以看到`mLayoutMode = -1` ，表明布局已经离开屏幕了，此实例存在内存泄漏的情况

![hprof分析-message持有view](http://cdn.yangchaofan.cn/typora/hprof分析-message持有view.jpg)

接着我们查看`References`区域，逐级点开我们发现`Handler`发送的`Message`持有了当前`view`，导致`view`在离开窗口的时候，无法被垃圾回收器回收。

右键点击查看问题代码

![image-20221115164344291](http://cdn.yangchaofan.cn/typora/image-20221115164344291.png)

问题代码：

```java
    playHandler.post(new Runnable() {
                @Override
                public void run() {
                    tv_play.setText(playItem.getProgramTitle());
                    tb_play.setSelected(true);
                    initView();
                }
            });
```

看到`new Runnbale`，这是是匿名内部类，[匿名内部类持有当前类的引用](https://blog.csdn.net/cmder1000/article/details/108091460)，匿名`Runnbale`未执行完毕，`Runnbale`内存未释放的时候，`view`就无法被释放，而匿名`Runnbale`的释放时机不可控，由`Handler`、`Looper`、`Runnbale`执行情况影响。

那么我们该怎么优化呢？

2. 使用非匿名或静态的`Handler`+弱引用，处理此任务
3. 在主线程处理此任务
4. `view`退出的时候释放`Message`对`view`的引用

**笔者采用了方案3：**

```java
tv_play.setText(playItem.getProgramTitle());
tb_play.setSelected(true);
initView();
```

遇到了异常提示非UI线程更新UI，可知该代码块默认在子线程执行

```java
ViewRootImpl: Accessibility content change on non-UI thread. Future Android versions will throw an exception.
    android.view.ViewRootImpl$CalledFromWrongThreadException: Only the original thread that created a view hierarchy can touch its views.
```

遂作罢放弃方案2，方案1代码与下面view的第三个实例写法一致，不重复写了；我们解释一下方案3：

> view退出的时候释放Message对view的引用

根据上图所示，我们看到`Message-Runnbale-View`的引用关系可知，`Looper`中的`Message`持续的引用`view`，我们最高效释放内存的做法是`view`离开窗口的时候，斩断`Message`与`view`的引用关系，那么我们该怎么做呢？答案是：

1. 结束子线程任务
2. 清空`Looper`缓存的`Message`
3. 释放`Handler`

第一步：结束子线程任务很简单

```java
thread.interrupt()
```

本案例给`Handler`传入的是`Runnbale`，`Handler`未提供结束`Runnbale`的接口，此项优化搁置

第二步：清空`Message`

已知`Looper`提供了清空`Message`的接口

1. `Looper#quit`
2. `Looper#quitSafely`
3. 主线程的Looper无法退出

已知Handler提供了释放Message的接口

1. `Handler#removeCallbacksAndMessages`

那我们优化起来就很简单了，清空`Handler`持有的`Message`

```java
@Override
    protected void onDetachedFromWindow() {
        super.onDetachedFromWindow();
 		...
        // 释放message，断开message-Runnbale-view的引用链
        if (playHandler != null) {
            playHandler.removeCallbacksAndMessages(null);
            playHandler = null;
        }
    }
```

主线程调`quit`和`quitSafely`会抛异常,所以Looper相关API无法在主线程调用，不适合当前的例子，遂作罢：

    java.lang.IllegalStateException: Main thread not allowed to quit.
        at android.os.MessageQueue.quit(MessageQueue.java:417)
        at android.os.Looper.quitSafely(Looper.java:284)
        at com.unilife.common.floatwindow.view.MediaPlayerRight.onDetachedFromWindow(MediaPlayerRight.java:256)






我们继续看**view的第二个实例**

先查看Fields区域，观察`mLayoutmode`值，判断`view`是否离开了窗口，如果已经离开了窗口，表明view未被回收，存在内存泄漏

![hprof分析-view未回收](http://cdn.yangchaofan.cn/typora/hprof分析-view未回收.jpg)

可以看到`mLayoutMode = -1` ，表明布局已经离开屏幕了，此实例存在内存泄漏的情况

接着我们看References区域，观察调用链

![image-20221115180242196](http://cdn.yangchaofan.cn/typora/image-20221115180242196.png)

可以看到`MediaPlayerIml`有一个成员变量`mMediaPlayListenerCacheList`，缓存了`MediaPlayListener`，`MediaPlayListener`又是在`view`实例里面创建的,并且作为内部类，它持有`view`的实例。现在我们得到了清晰的调用链，`MediaPlayerIml->mMediaPlayListenerCacheList->MediaPlayListener->view`,`MediaPlayerIml`引用`view`导致`view`实例无法被释放

查看问题代码：

笔者发现`view#onDetachedFromWindow`已经触发了移除`list#listener`操作

```java
  @Override
    protected void onDetachedFromWindow() {
        super.onDetachedFromWindow();
        mediaPlayerIml.unregisterListener(playListener);
    }
```

可以看到内部实现是`remove`调引用的

```java
  /**
     * 取消注册listener
     *
     * @param listener
     */
    public synchronized void unregisterListener(MediaPlayListener listener) {
        mMediaPlayListenerCacheList.remove(listener);
    }

```

那为什么会未回收持续占用内存呢？

1. 抓拍`hprof`文件期间，代码未执行到`unregisterListener`，导致`view`内存未得到释放
2. `mMediaPlayListenerCacheList`添加的`listener`与`remove`的`listener`不是同一个
3. 此处没有产生内存泄漏,判断`view`是否应该被回收的依据有问题



搁置疑问，接着我们来看**view的第三个实例**，节省时间，笔者直接调到代码索引出，展示问题代码：

**view#非静态内部类**

```java
    /**
     * 播放进度条刷新控
     */
    private Handler m_handler = new Handler() {
        @Override
        public void handleMessage(Message msg) {
            super.handleMessage(msg);
            switch (msg.what) {
                case MSG_FLUSH_SEEKBAR:
                    boolean isPlaying = mediaPlayerIml != null && mediaPlayerIml.getPlayStatus() == QingtingConfig.PLAY;
                    if (isPlaying) {
                        int currentTime = mediaPlayerIml.getCurrentTime();
                        int totalTime = mediaPlayerIml.getTotalTime();
                        mSeekBar.setMax(totalTime);
                        mSeekBar.setProgress(currentTime);
                        mPrograssBar.setMaxProgress(totalTime);
                        mPrograssBar.setCurrentProgress(currentTime);
                        LoggerUtils.instance().logE("mediaPlayJindu", "mediaPlayJindu" + totalTime + "/" + currentTime);
                    }
                    m_handler.sendEmptyMessageDelayed(MSG_FLUSH_SEEKBAR, MSG_FLUSH_TIME);
                    break;
            }
        }
    };
```

可以看到此处还是使用了非静态内部类`m_handler`，`m_handler`持有当前`view` 的引用，`m_handler`如果长期存在，那么`view`的内存也不会被释放

解决方法如下：

1. 定义外部类`Handler`
2. 定义静态内部类
3. 定义静态内部类+弱引用

笔者采用了方案3：

定义静态内部类

```java
  private  static  class UpdateHandler extends Handler {
        private final WeakReference<MediaPlayerIml> mediaPlayerImlWeakReference;
        private final WeakReference<SeekBar> seekBarWeakReference;
        private final WeakReference<QQCircleProgressBar> progressBarWeakReference;

        public UpdateHandler(MediaPlayerIml mediaPlayerIml, SeekBar seekBar, QQCircleProgressBar progressBar) {
            mediaPlayerImlWeakReference = new WeakReference<MediaPlayerIml>(mediaPlayerIml);
            seekBarWeakReference = new WeakReference<SeekBar>(seekBar);
            progressBarWeakReference = new WeakReference<QQCircleProgressBar>(progressBar);
        }

        @Override
        public void handleMessage(Message msg) {
            super.handleMessage(msg);
            if (msg.what == MSG_FLUSH_SEEKBAR) {
                MediaPlayerIml mediaPlayerIml = mediaPlayerImlWeakReference.get();
                SeekBar seekBar = seekBarWeakReference.get();
                QQCircleProgressBar qqCircleProgressBar =progressBarWeakReference.get();
                boolean isPlaying = mediaPlayerIml != null && mediaPlayerIml.getPlayStatus() == QingtingConfig.PLAY;
                if (isPlaying && seekBar!=null && qqCircleProgressBar != null) {
                    int currentTime = mediaPlayerIml.getCurrentTime();
                    int totalTime = mediaPlayerIml.getTotalTime();
                    seekBar.setMax(totalTime);
                    seekBar.setProgress(currentTime);
                    qqCircleProgressBar.setMaxProgress(totalTime);
                    qqCircleProgressBar.setCurrentProgress(currentTime);
                    LoggerUtils.instance().logE("mediaPlayJindu", "mediaPlayJindu" + totalTime + "/" + currentTime);
                }
                sendEmptyMessageDelayed(MSG_FLUSH_SEEKBAR, MSG_FLUSH_TIME);
            }
        }
    }
```

在`view`使用时，初始化`handler`，构造参数传入组件id；

```java
m_handler = new UpdateHandler(MediaPlayerIml.getInstance(),mSeekBar,mPrograssBar);
m_handler.sendEmptyMessage(MSG_FLUSH_SEEKBAR);
```

在view离开窗口时候，销毁handler数据；

```java
    @Override
    protected void onDetachedFromWindow() {
        super.onDetachedFromWindow();
        ...
        if(m_handler!=null){
            m_handler.removeCallbacksAndMessages(null);
            m_handler = null;
        }
    }
```



总结我们针对此按理做的优化

1. 静态`Handler`+弱引用，释放了对`handler`对`view`的引用，让`view`及时销毁，view占据的内存及时被垃圾回收器释放
2. 释放了`Message`对`view`的引用,在view及时退出界面的时候，立即斩断`message`对`view`

回顾一下优化前的实例数量，多次操作,隐藏展示悬浮窗之后，内存中存在多份悬浮窗实例，之前创建过的悬浮窗内存一直无法被回收:

![hprof分析-view内存泄漏-优化前](http://cdn.yangchaofan.cn/typora/hprof分析-view内存泄漏-优化前.jpg)

优化后效果，多次操作，当屏幕上存在一个`view`时，只存在一份`view`实例：

![image-20221115152920373](http://cdn.yangchaofan.cn/typora/image-20221115152920373.png)

在`InstanceList`里可以看到`Instance`数量为1，同样的操作次数下，优化前占用内存280824byte；优化后750byte，内存减少至原先的$1/374$；由此可见，view的就及时回收，极大的节省了内存占用。



## 案例4——异步任务内存泄漏

异步任务，代指起子线程异步完成一些数据操作、网络接口请求等，通常会使用以下API：

1. `Runnbale`，`Thread`,线程池
2. `RxJava`
3. `HandlerThread`
4. `Timer+TimerTask`定时器
5. `AsyncTask`

而这些异步任务很有可能操作内存泄漏，下面我们以`Rxjava`为例，演示此问题，线程、线程池的问题也类似，就不再一一演示了。

大多数项目的网络基础库，传入Rxjava的是匿名`Observer`，任务过多时，未执行的任务的`Observer`会持有当前页面的引用，造成内存泄漏，演示出这个场景

那么问题来了

1. `rxjava`就会存在内存泄漏吗？
2. `subscribe`传入的匿名内部类`Consumer`实例不会造成内存泄漏吗？
3. 异步任务返回时，`Activity`已经处于`onDestroyed`状态，`Observer`持有``Activity`引用，`Activity`内存还能被回收吗？



我们来验证一下`rxjava`的泄漏场景：

假设我们在`Activity#onResume`方法里，写了异步任务，任务结束后，设置view的属性，在任务结束之前，我们会调用`Activity#finsh`操作退出当前页面，如下坨屎：页面在12秒后实际已经处于`onDestroyed状态了

> 为了演示问题，我将延时时间增大，写成12秒，模拟异步任务返回的情况

```java
        Observable.timer(12000, TimeUnit.MILLISECONDS)
                .subscribeOn(AndroidSchedulers.mainThread())
                .subscribe(new Consumer<Long>() {
                    @Override
                    public void accept(Long aLong) throws Exception {
                        dataBinding.layoutWelcome.setVisibility(View.GONE);
                        ...其他view 的引用
                    }
                });
```

测试步骤为：进入`Activity`-立刻退出`Activity`-一段时间之后观察`Activity`的内存是否被回收

我们得到一份`hprof`文件，来分析下

![hprof分析-rxjava内存泄漏1](http://cdn.yangchaofan.cn/typora/hprof分析-rxjava内存泄漏1.jpg)

老规矩先看下`Instance-Details-Instance`区域，`Activity`的生命周期`onDestroyed`的值是否为`true`，按步骤点击一看，确实为true，证明`Activity`已经离开窗口了，处于销毁的生命周期中，我们期望的时候垃圾回收器可以回收Activity占据的内存，但事实上我们在Hprof文件看到了，表明`Activity`占据的内存未回收。

紧着着我们面临下一个问题，如何找到导致Activity内存泄漏的原因呢？谁引用了`Activity`？

点击`Instance-Details-References`区域，我们可以很快得到答案，按步骤点击`Jump to Source`![hprof分析-rxjava内存泄漏2-定位代码位置](http://cdn.yangchaofan.cn/typora/hprof分析-rxjava内存泄漏2-定位代码位置.jpg)

果然，立刻跳转到内存泄漏所在的代码块，终于我们通过分析hprof文件找到了问题所在:

![image-20221118150623915](http://cdn.yangchaofan.cn/typora/image-20221118150623915.png)

那么如何解决此问题呢？`rxjava`提供了`CompositeDisposable`解决此类泄漏问题，做法如下：

1. 创建实例对象

```java
    /**
     * 管理rxjava的任务，及时释放，不执行emitter#onNext
     */
    public CompositeDisposable compositeDisposable = new CompositeDisposable();
```

2. 用`compositeDisposable`实例去控制任务的生命周期

```java
   compositeDisposable.add(Observable.timer(12000, TimeUnit.MILLISECONDS)
                .subscribeOn(AndroidSchedulers.mainThread())
                .subscribe(new Consumer<Long>() {
                    @Override
                    public void accept(Long aLong) throws Exception {
                        dataBinding.layoutWelcome.setVisibility(View.GONE);
                        processIntent(getIntent());
                    }
                }));
```

3. 页面生命周期`onDestroyed`期间清空任务

```java
    @Override
    protected void onDestroy() {
        super.onDestroy();
        compositeDisposable.clear();
    }
```

优化后的效果：

![hprof分析-rxjava内存泄漏-优化后](http://cdn.yangchaofan.cn/typora/hprof分析-rxjava内存泄漏-优化后.jpg)

优化后可看到`Depth`为空，`GC root` 为空，表明没有其他实例引用`Activity`了，当垃圾回收器扫描到此实例，该实例内存会被回收。





还记得开头的问题吗？

1. `rxjava`就会存在内存泄漏吗？答：会存在，`consumer`作为`Activity`的内部类，持有当前`Activity`的引用，任务未结束，`Activity`已销毁就会出现内存泄漏
2. `subscribe`传入的匿名内部类`Consumer`实例不会造成内存泄漏吗？答：只要是匿名内部类，就很有可能内存泄漏，上例子已经证明会产生内存问题。
3. 异步任务返回时，`Activity`已经处于`onDestroyed`状态，`Observer`持有``Activity`引用，`Activity`内存还能被回收吗？答：无法被回收



## 案例5——单例类

单例与生命周期实例

错误写法：传入Activity/fragment

正确写法：

1、不通过外部传入context，内部直接访问Application的公有方法，得到context

2、在构造函数内`context.getApplicationContext`





多线程单例

多线程单例问题

多进程单例问题

究极方案：

静态内部类



## 案例6——匿名内部类

前面的例子有许多

## 案例7——Handler

Handler的写法本质也是定义了一个内部类，初始化了内部类的实例对象而已

## 案例8——资源未关闭

广播-未注册

EventBus-解注册

Service-尽量停止stopService,忘记stop会内存泄漏

AIDL-远程service解绑

SQL、IO、File及时关闭



## 案例9——静态变量





## 案例10——集合

未及时remove集合内对象或clear集合中的实例



## 案例11——动画

1、属性动画

2、轮播viewPager

## 案例12——Webview

动态创建而非xml创建——避免持有context

父view移除webview之后，再释放webview，置为空，主动调用onDestroyed，而非webview直接释放

webview的成员属性也要主动释放：如loading、history资源、webview的子view

## 案例13——SurfaceView

置为null是不够的，不会触发view的onDestroyed，必须设置为不可见



## 案例14——Bitmap

Bitmap-仅置为空不够，native内存需要recycle才能回收

bitmap内存优化可以看笔者的其他文章



## 片段式的经验：

1. 静态内部类多次创建，有什么问题吗？-不确定
2. 静态内部类的实例，需要手动释放吗？-不确定
3. 活用onLowMemory、onTrimMemory，做好内存容灾机制
4. UI不可见的时候，及时释放UI引用的资源，大到Activity、Fragment、Dialog、Window，小到一个自定义View，都要管理好view本身和view引用的对象
5. Bitmap要用好：裁剪尺寸等比压缩或采样率压缩、压缩质量、复用内存、LRUcache缓存已加载图片
6. 少用枚举
7. 多用优化过的数据结构，SparesArray
8. 使用多进程：独立的任务放到后台进程里，如定位、推送、保活、Webview；注意避免Application多次初始化
9. 有些对象不是置为null就完事的，如webview、surfaceview、bitmap、属性动画
10. 仍未找到准确的原生属性能够判定view是否离开窗口
11. 静态类要调用一些非静态方法，可能需要扩展更多的弱引用实例通过静态类构造函数传进来
12. 跨App跳转，很难避免标准模式的`Activity`不被多次创建，但是可以做到Activity退出一次，就释放一次Activity内存，这是基本要求
13. 静态内部类使用某些实例前，可能需要判断该实例是否为空——在异步场景很容易出现空指针
14. 尽量不要在`xml`中定义一些`view`，如`TextureView`、`Surfaceview`，他们默认是与`Activity`绑定的，他们的构造函数传入的`context`是`Activity`实例，有可能造成内存泄漏,最好改为代码动态创建这些复杂的view
15. glide为什么会内存泄漏？
16. MVP为什么会内存泄漏？参考DeviceSetFragment的泄漏点3例子
17. `context`被`view`持有，`view`需要及时释放对`context`的引用，释放方式为`view = null`；这样就可以减少指向`context`的引用了；当指向`context`的引用总数为`0`，`context（Activity）`就会被回收了
18. 有些内存问题需要特定的条件才能出现，如开启关闭摄像头、输入搜索框之后清空搜索框、先进入B页面再回退刷新A页面，monkey无法替代人来操作这些步骤，人为点击还是有意义的
19. 发现一个泄漏点，解决完之后，再重复测试，才有可能发现下一个泄漏点，只有解决第一个泄漏点，才能发现其他未出现的泄漏点
20. 优化完之后可能会发现原先无法复现的新问题，优化之后要继续进行大量的重复测试，直到确保不出现泄漏情况才能结束优化工作



待求证的疑问

1. `System.exit(0)`杀死进程，能释放该进程下的所有内存吗？
2. 非静态内部类持有当前类的引用，还是非静态内部类的实例持有当前类的引用？
3. 有必要把所有的内部类都定义成静态吗？参考[java - Java 内部类有坑。。100 % 内存泄露！_个人文章 - SegmentFault 思否](https://segmentfault.com/a/1190000042870246#item-6)、[(8 封私信 / 80 条消息) 为什么Java内部类要设计成静态和非静态两种？ - 知乎 (zhihu.com)](https://www.zhihu.com/question/28197253)
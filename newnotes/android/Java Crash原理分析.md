# Java Crash原理分析





# 前言

**研究的对象**是<font color="blue">APP进程</font>

**研究的问题**主体是 <font color="blue">APP为什么会Crash</font>

**研究的路线**是：从APP进程被Crash的第一行代码开始溯源，`Java->JVM->Android平台->Android系统->Devices`，直至我们最后能看懂的地方。

**研究的工具**是：

1. Source Insight 4.0
2. UML
3. Logcat
4. Logger
5. MtkLog
6. SysTrace
7. TraceView
8. Profiler
9. Leakcanary
10. Blockcanary
11. Buggly
12. 友盟
13. 自建APM

**研究的参考资料**是：

1. Java JRE SRC
2. Android Framwork SRC
3. 工具对应的文档



# App异常4个现象

>  **有这么4种现象**

| 现象名称            |                                                              | 原因                                 |
| ------------------- | ------------------------------------------------------------ | ------------------------------------ |
| App已停止运行       | ![image-20210423143651100](http://cdn.yangchaofan.cn//typora/image-20210423143651100.png) | RuntimeException<br>OutOfMemoryError |
| App闪退             | 黑屏并回到Launcher首页                                       | StackOverflowError<br>               |
| App屡次停止运行     | ![image-20210423143600543](http://cdn.yangchaofan.cn//typora/image-20210423143600543.png) | 多次异常                             |
| （补充）App没有响应 | <img src="http://cdn.yangchaofan.cn/typora/image-20210426163446282.png" alt="image-20210426163446282" style="zoom:80%;" /> |                                      |

# 1个Demo

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="http://cdn.yangchaofan.cn/typora/image-20210426190621477.png" width = "60%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图1-Demo展示
      </div>
</center>


> 根据Crash源头不同,Crash级别可以分为以下4类

1. JVM：Throwable类问题
2. OS平台：内存、文件流、中断等
3. 硬件设备：路由器、宽带、设备重启、摄像头、内存卡、磁盘等
4. 物理环境：水、温度等

# Java级别Crash原理

## Java Crsh定义

**什么是Java级别的Crash？**

1. `JavaAPP`或`AndroidAPP`运行过程中，发生了`Throwable`类问题,被JVM识别捕获
2. `JVM`主动操作导致程序退出，而非Android平台OS、硬件设备、物理环境导致程序退出

**关于Java级别Crash主要研究哪些问题？**

> 研究对象：Java最小任务单位Thread
>
> 研究场景：JavaAPP程序、AndroidAPP程序
>
> 场景区别：Java虚拟机、Android虚拟机
>
> 研究问题：
>
> 1. Crash杀死了谁？连带杀死了谁？-Thread\ThreadGroup\Process
> 2. 谁Crash了谁？谁杀死了APP？- Runtime
> 3. 停止运行Dialog是谁调用弹出的？-
> 4. Exception抛出流程?-Exception对象是x创建的，第一个抛出者是xx
> 5. Logcat打印流程?-每一行ExceptionLog是xx、xx打印的

## 研究对象Thread

> 主要回答一个问题：Crash杀死的是谁？——运行APP的最小单位是Thread（Process），所以杀死的是当前Thread

### Thread基础知识

#### Thread[^4]

> 代码位置：jdk\src\java\lang\Thread.java

```java
 	/**
     * 将未捕获的异常分派给处理程序。这个方法只打算由JVM调用。
     */
    private void dispatchUncaughtException(Throwable e) {
        getUncaughtExceptionHandler().uncaughtException(this, e);
    }
...
	/* 这个线程所在的组 */
    private ThreadGroup group;
	 // 默认null 除非显示设置
    private volatile UncaughtExceptionHandler uncaughtExceptionHandler;

    /**
     * 返回当此线程由于未捕获异常而突然终止时调用的处理程序。
     * 如果该线程没有显式设置未捕获的异常处理程序，则返回该线程的ThreadGroup对象，
     * 除非该线程已经终止，在这种情 况下返回null。为这个线程返回未捕获的异常处理程序
     */
    public UncaughtExceptionHandler getUncaughtExceptionHandler() {
        return uncaughtExceptionHandler != null ?
            uncaughtExceptionHandler : group;
    }
...
 /*
     * Thread内部接口
     * 用于在线程由于未捕获异常而突然终止时调用的处理程序。
     * 当一个线程由于未捕获的异常而即将终止时，Java虚拟机将使用getUncaughtExceptionHandler查询该线程的
     * UncaughtExceptionHandler，并调用该处理程序的uncaughtException方法，将线程和异常作为参数传递。
     * 
     * 如果一个线程没有显式设置它的UncaughtExceptionHandler，那么它的ThreadGroup对象就充当它的UncaughtExceptionHand
     * 如果ThreadGroup对象对处理异常没有特殊要求，它可以将调用转发给getDefaultUncaughtExceptionHandler默认未捕获的异常处理程序
     */
    @FunctionalInterface
    public interface UncaughtExceptionHandler {
        /**
         * 方法在给定线程由于给定未捕获异常而终止时调用。 Java虚拟机将忽略此方法引发的任何异常。
         */
        void uncaughtException(Thread t, Throwable e);
    }
```

JVM执行Thread对象时，处理异常流程为:

1. JVM在执行当前`Thread`对象时遇到了异常并进行抛出，如果JVM发现开发者并未对该异常进行捕获，则JVM会调用`Thread`的`dispatchUncaughtException`方法将Exception对象传递给`Thread`持有的`UncaughtExceptionHandler `
2. 如果`Thread`持有的`UncaughtExceptionHandler` 不为空，则交由`Thread`对象持有的UncaughtExceptionHandler 对象处理；
3. 如果`Thread`持有的`UncaughtExceptionHandler `为空，则交给`ThreadGroup`来处理`Thread`所抛出`Exception`对象

#### ThreadGroup[^4]

> 代码位置：jdk\src\java\lang\ThreadGroup.java

```java

class ThreadGroup implements Thread.UncaughtExceptionHandler {
    
    ...
     /**
     * ThreadGroup.java
     * 当此线程组中的线程因未捕获异常而停止，且该线程没有特定的Thread.UncaughtExceptionHandler设置时，由Java虚拟机调用。。
     * ThreadGroup的uncaughtException方法执行如下操作:
     * 如果这个线程组有一个父线程组，该父线程组的uncaughtException方法将被调用，并带有相同的两个参数。
     * 否则，这个方法会检查是否安装了一个默认的未捕获异常处理程序，如果安装了，它的uncaughtException方法会被调用，并带有相同的两个参数。
     * 否则，此方法将确定Throwable参数是否为ThreadDeath的实例。如果是这样，也没有做什么特别的事情。否则，包含线程名称的消息(从线程的getName方法返回)和堆栈回溯(使用Throwable的printStackTrace方法)将打印到标准错误流。
     */
    public void uncaughtException(Thread t, Throwable e) {
        if (parent != null) {
            parent.uncaughtException(t, e);
        } else {
            Thread.UncaughtExceptionHandler ueh =
                Thread.getDefaultUncaughtExceptionHandler();
            if (ueh != null) {
                ueh.uncaughtException(t, e);
            } else if (!(e instanceof ThreadDeath)) {
                System.err.print("Exception in thread \""
                                 + t.getName() + "\" ");
                e.printStackTrace(System.err);
            }
        }
    }

}
```

`ThreadGroup`处理`uncaughtException`的流程很简洁

1. 默认情况，先将Exception对象交给`父ThreadGroup`处理
2. 判断Thread是否持有`defaultUncaughtExceptionHandler`默认异常处理器
3. 如果有默认异常处理器，则交由其处理
4. 如果没有异常处理器，则将错误信息输出到`System.err`

> 总结一下Exception处理顺序，假设某个Thread抛出Exception
>
> 消费Exception的顺序为
>
> 1. Thread
>    1. `UncaughtExceptionHandler` 
> 2. ThreadGroup
>    1. ParentThread
>       1. `DefaultUncaughtExceptionHandler`
>       2. `UncaughtExceptionHandler` 
>
> 

### 主线程

#### JavaAPP

```java
public class CrashTest {
    public static void main(String[] args) {
        testMainTrheadCrash();
    }
  /**
     * 测试主线程异常Crash
     */
    public static void testMainTrheadCrash() {
        Thread.currentThread().setName("iron man is on the main thread");
        int a = 3;
        int b = a / 0;
    }
      
 }
```

异常堆栈

```java
Exception in thread "iron man is on the main thread" java.lang.ArithmeticException: / by zero
        at CrashTest.testMainTrheadCrash(CrashTest.java:29)
        at CrashTest.main(CrashTest.java:4)
```
可以看到线程名是主线程`main`
#### AndroidAPP

```java
	/**
     * main thread throws exceptions
     * @param view
     */
    public void createExceptionInMainThread(View view) {
        Thread.currentThread().setName("iron man is on the android main thread");
        int a = 10;
        int b = 0;
        int c = a / b;
    }
```

异常堆栈

```java
2021-04-25 14:33:38.372 4707-4707/com.work.demotest E/AndroidRuntime: FATAL EXCEPTION: iron man is on the android main thread
    Process: com.work.demotest, PID: 4707
    java.lang.IllegalStateException: Could not execute method for android:onClick
        at android.view.View$DeclaredOnClickListener.onClick(View.java:4726)
        at android.view.View.performClick(View.java:5638)
        at android.view.View$PerformClick.run(View.java:22430)
        at android.os.Handler.handleCallback(Handler.java:751)
        at android.os.Handler.dispatchMessage(Handler.java:95)
        at android.os.Looper.loop(Looper.java:154)
        at android.app.ActivityThread.main(ActivityThread.java:6198)
        at java.lang.reflect.Method.invoke(Native Method)
        at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:891)
        at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:781)
     Caused by: java.lang.reflect.InvocationTargetException
        at java.lang.reflect.Method.invoke(Native Method)
        at android.view.View$DeclaredOnClickListener.onClick(View.java:4721)
        at android.view.View.performClick(View.java:5638) 
        at android.view.View$PerformClick.run(View.java:22430) 
        at android.os.Handler.handleCallback(Handler.java:751) 
        at android.os.Handler.dispatchMessage(Handler.java:95) 
        at android.os.Looper.loop(Looper.java:154) 
        at android.app.ActivityThread.main(ActivityThread.java:6198) 
        at java.lang.reflect.Method.invoke(Native Method) 
        at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:891) 
        at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:781) 
     Caused by: java.lang.ArithmeticException: divide by zero
        at com.work.demotest.TestExceptionActivity.createExceptionInMainThread(TestExceptionActivity.java:132)
        at java.lang.reflect.Method.invoke(Native Method) 
        at android.view.View$DeclaredOnClickListener.onClick(View.java:4721) 
        at android.view.View.performClick(View.java:5638) 
        at android.view.View$PerformClick.run(View.java:22430) 
        at android.os.Handler.handleCallback(Handler.java:751) 
        at android.os.Handler.dispatchMessage(Handler.java:95) 
        at android.os.Looper.loop(Looper.java:154) 
        at android.app.ActivityThread.main(ActivityThread.java:6198) 
        at java.lang.reflect.Method.invoke(Native Method) 
        at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:891) 
        at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:781) 

```



### 子线程

#### JavaAPP

```java
	/**
     * 测试子线程异常
     */
    public static void testChildThreadCrash() {
        Thread childTrhead = new Thread(new Runnable() {
            @Override
            public void run() {
                // TODO Auto-generated method stub
                int a = 3;
                int b = a / 0;
            }
        });
        childTrhead.setName("iron man is on the child thread");
        childTrhead.start();
    }
```

异常堆栈

```java
Exception in thread "iron man is on the child thread" java.lang.ArithmeticException: / by zero
        at CrashTest$1.run(CrashTest.java:16)
        at java.lang.Thread.run(Thread.java:745)
```

#### AndroidAPP

```java
/**
 * main thread  dose not catch sub thread exception
 * @param view
 */
public void createExceptionInThread(View view) {
    try {
        new Thread(new Runnable() {
            @Override
            public void run() {
                int a = 10;
                int b = 0;
                int c = a / b;
            }
        }).start();
    }catch (Exception e){
        Log.d("createExceptionInThread","main thread is catch sub thread exception");
    }
}
```

异常堆栈

```java
2021-04-25 14:35:25.580 4775-4811/com.work.demotest E/AndroidRuntime: FATAL EXCEPTION: iron man is on the android chilid thread
    Process: com.work.demotest, PID: 4775
    java.lang.ArithmeticException: divide by zero
        at com.work.demotest.TestExceptionActivity$1.run(TestExceptionActivity.java:113)
        at java.lang.Thread.run(Thread.java:761)

```

> JavaAPP与AndroidAPP默认异常堆栈区别

| 命令行默认异常堆栈 | JavaAPP                       | AndroidAPP         |
| ------------------ | ----------------------------- | ------------------ |
| Process            | :negative_squared_cross_mark: | :heavy_check_mark: |
| Thread             | :heavy_check_mark:            | :heavy_check_mark: |
| Exception          | :heavy_check_mark:            | :heavy_check_mark: |

> Android主线程与子线程异常堆栈区别

| Android异常堆栈 | Process | Thread                               | view | ActivityThread | ZygoteInit |
| --------------- | ------- | ------------------------------------ | ---- | ---- | ---- |
| ChildThread     | :heavy_check_mark: | :heavy_check_mark: |  :negative_squared_cross_mark:    |  :negative_squared_cross_mark:  |  :negative_squared_cross_mark:  |
| MainThread      | :heavy_check_mark:        | :heavy_check_mark: |  :heavy_check_mark:    | :heavy_check_mark: |  :heavy_check_mark:  |

> 疑问：Android主线程异常，为什么抛出如此多的信息？为什么有这种区别？

答：因为AndroidAPP的主线程所做的任务位于`ActivityThread.main`中，在`main`方法中做了比子线程更多的任务。



## 研究对象Runtime[^3]

> 主要回答一个问题，谁杀死了APP？

### JavaRuntime

> 代码位置：jre/java/lang/Runtime 
>
> 调用者：暂时理解为虚拟机(没有查到准确的触发者)
>
> 代码用途：杀死JavaAPP、运行JavaAPP

```java
/**
* 每个Java应用程序都有一个Runtime类实例，
* 该类允许应用程序与应用程序运行的环境交互。当前运行时可以从getRuntime方法获得。
* 应用程序不能创建自己的此类实例。
*/
public class Runtime {  
	...
	public void exit(int status) {
        SecurityManager security = System.getSecurityManager();
        if (security != null) {
            security.checkExit(status);
        }
        Shutdown.exit(status);
    }
}
```



### AndroidRuntime[^2]

> 代码位置: /frameworks/base/core/java/com/android/internal/os/RuntimeInit.java
>
> 调用者：暂时理解为虚拟机(没有查到准确的触发者)
>
> 代码用途：运行AndroidAPP、杀死AndroidAPP、打印AndroidAPP运行过程的Log

```java
/**
 * 运行时初始化的主入口点。不供公共访问。
 */
public class RuntimeInit {
    /**
    * main方法入口
    */
 	public static final void main(String[] argv) {
		commonInit();
        nativeFinishInit();
    }
    protected static final void commonInit() {
    /*
     * 设置虚拟机中的线程未捕获异常处理程序。APP可以替换默认的未捕获异常处
     * 理器，但不能替换预处理处理器
     */
    LoggingHandler loggingHandler = new LoggingHandler();
    Thread.setUncaughtExceptionPreHandler(loggingHandler);
    Thread.setDefaultUncaughtExceptionHandler(new KillApplicationHandler(loggingHandler));

    initialized = true;
	}
    /**
     * RuntimeInit内部类
     * 处理未捕获异常导致的应用程序死亡 framework负责为主线程捕获这些信息
     * 因此这应该只对应用程序创建的线程有影响。
     * 在此方法运行之前，loginghandler将已经有日志细节。
     */
    private static class KillApplicationHandler implements Thread.UncaughtExceptionHandler {
        public void uncaughtException(Thread t, Throwable e) {
            try {
                // 如果已经报告Crash，避免死循环
                if (mCrashing)
                    return;
                mCrashing = true;

                // 尝试结束Profiling，如果Profiling正在运行，且我们杀死了正在Profiling的进程，内存中的缓冲区将会丢失
                // 请尝试停止Profiling，用来刷新buffer缓冲区。
                // 这样做可以提升方法追踪（method trace）结果，对调试Crash很有用
                if (ActivityThread.currentActivityThread() != null) {
                    ActivityThread.currentActivityThread().stopProfiling();
                }

                // 打开崩溃对话框，直到它消失
                ActivityManager.getService().handleApplicationCrash(mApplicationObject,
                        new ApplicationErrorReport.ParcelableCrashInfo(e));
            } catch (Throwable t2) {
                if (t2 instanceof DeadObjectException) {
                    // 系统进程已死，忽略执行
                } else {
                    try {
                        Clog_e(TAG, "Error reporting crash", t2);
                    } catch (Throwable t3) {
                        // Clog_e() 也存在异常哦
                    }
                }
            } finally {
                // 保障这个过程能执行
                Process.killProcess(Process.myPid());
                System.exit(10);
            }
        }
    }
    
}
```

总结下AppRuntime处理流程

1. `main`调用了`commonInit`
2. `commonInit`中设置了`setUncaughtExceptionPreHandler`、`setDefaultUncaughtExceptionHandler`
   1. 未捕获异常处理器`setUncaughtExceptionPreHandler`设置了`LoggingHandler`
   2. 默认未捕获异常处理器`setDefaultUncaughtExceptionHandler`设置了`KillApplicationHandler`

> 观察步骤2-b 这也就是为什么在App中设置默认异常捕获器可以防止APP闪退的原因

Runtime研究的启示有以下几点：

1. 无论是JavaAPP或是AndroidAPP，只要为Thread设置了未捕获异常处理器，就可以降低Crash的几率
2. 在未捕获异常处理器中可以做很多任务：异常信息持久化，上报日志，单个线程设置处理器，APP容灾处理
3. 主线程的默认未捕获异常处理器是`KillApplicationHandler`，默认会杀死当前进程

如果我们追溯代码，可以看到停止运行的弹出框的数据格式为`ApplicationErrorReport#CrashInfo`

> 代码位置:frameworks/base/core/java/android/app/ApplicationErrorReport.java
>
> 代码用途：创建Crash数据对象，保存命令行打印的错误信息

| 内部类名称  | 用途          |
| ----------- | ------------- |
| CrashInfo   | 描述Crash信息 |
| AnrInfo     | 描述ANR信息   |
| BatteryInfo | 描述电池信息  |



>  代码位置ActivityManagerService
>
> 代码用途：调出AppErrors的方法停止运行弹出框Dialog

```java
public class ActivityManagerService extends IActivityManager.Stub{
	final AppErrors mAppErrors; 
@Override
    public void crashApplication(int uid, int initialPid, String packageName, int userId,
            String message) {
        synchronized(this) {
            mAppErrors.scheduleAppCrashLocked(uid, initialPid, packageName, userId, message);
        }
    }
    
  }
```

> 代码位置AppErrors
>
> 代码用途：Dialog工具类，如创建停止运行弹出框Dialog

```java
class AppErrors {
    handleShowAppErrorUi(){
        proc.crashDialog = new AppErrorDialog(mContext, mService, data);
        ...
        data.proc.crashDialog.show();
    }   
    /**
    * 显示“unexpected error” dialog
    */
     void crashApplication(ProcessRecord r, ApplicationErrorReport.CrashInfo crashInfo) {
         ...
     }
    /**
    * 显示"anr" dialog
    */
    final void appNotResponding(ProcessRecord app, ActivityRecord activity,
            ActivityRecord parent, boolean aboveSystem, final String annotation){
           ...
           // 0 == show dialog, 1 = keep waiting, -1 = kill process immediately
           int res = mService.mController.appNotResponding(
                        app.processName, app.pid, info.toString());
           ...
            // 设置APP anr状态吗，锁住errorReportReceiver
            makeAppNotRespondingLocked(app,
                    activity != null ? activity.shortComponentName : null,
                    annotation != null ? "ANR " + annotation : "ANR",
                    info.toString());
     }
}
```

> 代码位置AppErrorDialog
>
> 代码用途：弹出框Dialog

```java
final class AppErrorDialog extends BaseErrorDialog implements View.OnClickListener {
     // 消息事件 'what' codes
    static final int FORCE_QUIT = 1;
    static final int FORCE_QUIT_AND_REPORT = 2;
    static final int RESTART = 3;
    static final int MUTE = 5;
    static final int TIMEOUT = 6;
    static final int CANCEL = 7;
}
```



### Java未捕获异常处理器

`CrashTest`测试类，类中定义了内部类`ExceptionHandler`

```java
public class CrashTest {
    public static void main(final String[] args) {
        // testChildThreadCrash();
        testMainTrheadCrash();
   }
   /** 
     * 内部类形式的处理器
     * 线程未捕获异常处理器
     */
    static class ExceptionHandler implements UncaughtExceptionHandler {

        public void uncaughtException(final Thread t, final Throwable e) {
            System.out.printf(t.getName()+",captured a exception \n");
            System.out.printf("thread error msg is"+e.toString());

        }
    }
}
```



#### 主线程

```java

    /**
     * 测试主线程异常Crash
     */
    public static void testMainTrheadCrash() {
        Thread.currentThread().setName("iron man is on the main thread");
        ExceptionHandler cExceptionHandler = new ExceptionHandler();
        Thread.currentThread().setUncaughtExceptionHandler(cExceptionHandler);
        final int a = 3;
        final int b = a / 0;
    }

```

异常日志

```java
iron man is on the main thread,captured a exception
thread error msg isjava.lang.ArithmeticException: / by zero
```

#### 子线程

```java
  /**
     * 测试子线程异常
     */
    public static void testChildThreadCrash() {
        final Thread childTrhead = new Thread(new Runnable() {
            @Override
            public void run() {
                // TODO Auto-generated method stub
                final int a = 3;
                final int b = a / 0;
            }
        });
        childTrhead.setName("iron man is on the child thread");
        ExceptionHandler cExceptionHandler = new ExceptionHandler();
        childTrhead.setUncaughtExceptionHandler(cExceptionHandler);
        childTrhead.start();
    }
```

异常日志

```java
iron man is on the child thread,captured a exception
thread error msg isjava.lang.ArithmeticException: / by zero
```

### Android未捕获异常处理器

#### 主线程

定义`Application`

```xml
   <application
        android:name=".collections.App"
      	>
   
    </application>
```

`App`中定义未捕获异常处理器，未开启默认未捕获异常处理

```java
public class App extends Application {
    /**
     * 线程未捕获异常处理器
     */
    static class ExceptionHandler implements Thread.UncaughtExceptionHandler {

        @Override
        public void uncaughtException(final Thread t, final Throwable e) {
            Log.d("uncaughtException",t.getName()+",captured a exception");
            Log.d("uncaughtException","thread error msg is"+e.toString());

        }
    }
    @Override
    public void onCreate() {
        super.onCreate();
        Thread.currentThread().setName("android app ");
        Thread.currentThread().setUncaughtExceptionHandler(new ExceptionHandler());
        //        Thread.setDefaultUncaughtExceptionHandler(new ExceptionHandler());
//        Thread.setDefaultUncaughtExceptionHandler(null);
    }
}
```

> 问题：setUncaughtExceptionHandler是否能高枕无忧？保证不Crash？

答：<font color="white">不能</font>


#### 测试主线程运行时异常

> 依然用Thread#子线程#AndroidAPP的代码演示

```java
    /**
     * main thread throws exceptions
     * @param view
     */
    public void createExceptionInMainThread(View view) {
        Thread.currentThread().setName("iron man is on the android main thread");
        int a = 10;
        int b = 0;
        int c = a / b;
    }
```

日志堆栈

```java
2021-04-26 10:36:24.678 7136-7136/com.work.demotest D/ExceptionHandler: uncaughtExceptioniron man is on the android main threadhas captured a exception
2021-04-26 10:36:24.678 7136-7136/com.work.demotest D/ExceptionHandler: uncaughtException has capture thread error ,msg isjava.lang.IllegalStateException: Could not execute method for android:onClick
2021-04-26 10:36:24.733 7166-7166/? D/ExceptionHandler: NoneUeCrashActivity onCreate

```

可以看到主线程的`Exception`被`未捕获异常处理器`处理了，并且跳转到了默认页面`NoneUeCrashActivity`，无异常堆栈。

#### 测试子线程运行时异常

```java
  /**
     * main thread  dose not catch sub thread exception
     *
     * @param view
     */
    public void createExceptionInThread(View view) {
        try {
           Thread thread =  new Thread(new Runnable() {
                @Override
                public void run() {
                    int a = 10;
                    int b = 0;
                    int c = a / b;
                }
            });
            thread.setName("iron man is on the android chilid thread");
            thread.start();
        } catch (Exception e) {
            Log.d("createExceptionInThread", "main thread is catch sub thread exception");
        }
    }

```

日志堆栈

```java
2021-04-26 10:39:37.421 7409-7439/com.work.demotest E/AndroidRuntime: FATAL EXCEPTION: iron man is on the android chilid thread
    Process: com.work.demotest, PID: 7409
    java.lang.ArithmeticException: divide by zero
        at com.work.demotest.TestExceptionActivity$1.run(TestExceptionActivity.java:120)
        at java.lang.Thread.run(Thread.java:761)
```

可以看到异常堆栈，并且弹出了停止运行弹窗。



<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="http://cdn.yangchaofan.cn/typora/image-20210426104141555.png" width = "60%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图2-APP停止运行
      </div>
</center>







通过设置主、子线程的`setDefaultUncaughtExceptionHandler`和`setUncaughtExceptionHandler`，记录是否显示停止运行Dialog的结果如下：

| 是否显示`停止运行Dialog`                     | 主线程运行时异常              | 子线程运行时异常              |
| -------------------------------------------- | ----------------------------- | ----------------------------- |
| 主线程设置setUncaughtExceptionHandler        | :negative_squared_cross_mark: | :heavy_check_mark:            |
| 主线程设置setDefaultUncaughtExceptionHandler | :negative_squared_cross_mark: | :negative_squared_cross_mark: |
| 子线程设置setUncaughtExceptionHandler        | :negative_squared_cross_mark: | :negative_squared_cross_mark: |
| 子线程设置setDefaultUncaughtExceptionHandler | :negative_squared_cross_mark: | :negative_squared_cross_mark: |

可以发现：

1. 主线程仅设置setUncaughtExceptionHandler，如果主线程抛出异常，则不会闪退，也不会弹出Dialog；**如果子线程抛出异常，则App会闪退弹出Dialog;**
2. 主线程设置setDefaultUncaughtExceptionHandler，如果子线程抛出异常，则App不会闪退，也不会显示Dialog
3. 子线程设置setUncaughtExceptionHandler或子线程设置setDefaultUncaughtExceptionHandler都不会闪退，也不会弹出Dialog

> 问题：Thread.setDefaultUncaughtExceptionHandler能解决什么问题？

答：

<font color="white">RuntimeInit中为APP设置的俩异未捕获常处理器Thread.setUncaughtExceptionPreHandler(loggingHandler);
Thread.setDefaultUncaughtExceptionHandler(new KillApplicationHandler(loggingHandler));</font>

#### 未捕获异常处理器最佳实践

##### 制定容灾策略

1. 在当前页面对MVVM、MVP或MVC各个层级做以下操作：退出、恢复、重试、缓存

2. 跳转到业务首页，如登录流程某一步异常跳转到登录首页、视频通话页异常跳转到聊天详情页

3. 显示错误页或错误弹窗，如遇到OOM，Dialog显示`内存不足,请优化内存后再试`、遇到StackOverFlow,Dialog显示`程序异常，进入克服中心反馈`

4. 保存错误数据，定时上报服务器

   ...

##### 主线程和子线程作区分

1. 子线程设置未捕获异常处理器，执行自己的容灾恢复逻辑

```java
 /**
     * 线程未捕获异常处理器
     */
     class ChildExceptionHandler implements Thread.UncaughtExceptionHandler {
        @Override
        public void uncaughtException(final Thread t, final Throwable e) {
            Log.d("ChildExceptionHandler", "uncaughtException " + t.getName() + " has captured a exception");
            Log.d("ChildExceptionHandler", "uncaughtException has capture thread error ,msg is " + e.toString());
            startCrashPage();
        }

    }

    public void startCrashPage() {
        Intent intent = new Intent();
        intent.setAction("com.crash.demo.crash");
        intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        startActivity(intent);
        android.os.Process.killProcess(android.os.Process.myPid());
        System.exit(0);
    }
    /**
     * 测试子线程设置未捕获异常处理器
     */
    public void runtimeNoneUE(View view){
        try {
            Thread thread =  new Thread(new Runnable() {
                @Override
                public void run() {
                    int a = 10;
                    int b = 0;
                    int c = a / b;
                }
            });
            thread.setUncaughtExceptionHandler(new ChildExceptionHandler());
            thread.setName("iron man is on the android chilid thread");
            thread.start();
        } catch (Exception e) {
            Log.d("createExceptionInThread", "main thread is catch sub thread exception");
        }
    }

```

2. `App`中主线程设置未捕获异常处理器，覆盖默认未捕获异常处理

```java
Thread.currentThread().setName("android app ");
// 未捕获异常处理器
Thread.currentThread().setUncaughtExceptionHandler(new ExceptionHandler());
// 默认未捕获处理器赋值
Thread.setDefaultUncaughtExceptionHandler(new ExceptionHandler());
// 或 默认未捕获处理器赋值为null
Thread.setDefaultUncaughtExceptionHandler(null);
```



#### Crash容灾1.0

| 常见做法                                                     |
| ------------------------------------------------------------ |
| [开源库CockRoach](https://github.com/android-notes/Cockroach) |
| [开源库DefenseCrashApplication](https://github.com/xuuhaoo/DefenseCrash) |
| 方法块进行try catch finally、throws 、throw new              |
| 对UI主线程设置未捕获异常处理器、默认未捕获异常处理器         |
| 对子线程设置未捕获异常处理器、默认未捕获异常处理器           |

## 研究对象虚拟机

> 主要回答一个问题：谁调用App的main方法，并持有当前App主线程对象？
>
> Java主线程是的任务入口是Java类#main
>
> Anroidd主线程的任务入口是ActivityThread#main
>
> AndroidJVM类似，仅以JavaJVM举例

### JavaJVM

编写一段try-catch代码，如下

```java
public class ExceptionCode {
    public static void main(String[] args) {
        try{
            int result = 3/0;
        }catch(Exception exception){
            System.out.print("exception");
        }finally{
            System.out.print("finally");
        }
    }
}
```

编译为class文件

```java
javac ExceptionCode.class
```

编译为字节码文件

```java
javap -c ExceptionCode.class
```

查看字节码文件

```powershell
Compiled from "ExceptionCode.java"
public class ExceptionCode {
  public ExceptionCode();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: iconst_3
       1: iconst_0
       2: idiv
       3: istore_1
       4: getstatic     #2                  // Field java/lang/System.out:Ljava/io/PrintStream;
       7: ldc           #3                  // String finally
       9: invokevirtual #4                  // Method java/io/PrintStream.print:(Ljava/lang/String;)V
      12: goto          46
      15: astore_1
      16: getstatic     #2                  // Field java/lang/System.out:Ljava/io/PrintStream;
      19: ldc           #6                  // String exception
      21: invokevirtual #4                  // Method java/io/PrintStream.print:(Ljava/lang/String;)V
      24: getstatic     #2                  // Field java/lang/System.out:Ljava/io/PrintStream;
      27: ldc           #3                  // String finally
      29: invokevirtual #4                  // Method java/io/PrintStream.print:(Ljava/lang/String;)V
      32: goto          46
      35: astore_2
      36: getstatic     #2                  // Field java/lang/System.out:Ljava/io/PrintStream;
      39: ldc           #3                  // String finally
      41: invokevirtual #4                  // Method java/io/PrintStream.print:(Ljava/lang/String;)V
      44: aload_2
      45: athrow
      46: return
    Exception table:
       from    to  target type
           0     4    15   Class java/lang/Exception
           0     4    35   any
          15    24    35   any
}
```



### Exception Table 异常表

异常表中包含了一个或多个异常处理者(Exception Handler)的信息，这些信息包含如下

- from 可能发生异常的起始点
- to 可能发生异常的结束点
- target 上述from和to之前发生异常后的异常处理者的位置
- type 异常处理者处理的异常的类信息,包括具体的包名类名

这张表足够回答许多常见异常捕获类的问题，不要急，这些问题将在《Crash知识分享第二期》展开讨论。

在这一步我们仅需要知道，在编译阶段结束的时候，finally里面的部分会被复制两份至`try语句的结尾`和`catch语句的结尾`.

异常表定义了main方法拥有3种执行分支

- 0-4之间，发生了指定类型为Exception的异常，则会跳转到语句15
  - 接下来15-24之间，会执行打印`字符串Exception`、`字符串finally`的机器码
- 0-4之间，无法伦发生什么异常，都会跳转到语句15
  - 接下来15-24之间，会执行打印`字符串Exception`、`字符串finally`的机器码
- 15-24之间，无论发生什么异常，都会跳转到语句35
  - 接下来24-29之间，会执行打印`字符串finally`的机器码



> 那么异常表用在什么时候呢[^1] 

答案是异常发生的时候，当一个异常发生时

1.JVM会在当前出现异常的方法中，查找异常表，是否有合适的处理者来处理

2.如果当前方法异常表不为空，并且异常符合处理者的from和to节点，并且type也匹配，则JVM调用位于target的调用者来处理。

3.如果上一条未找到合理的处理者，则继续查找异常表中的剩余条目

4.如果当前方法的异常表无法处理，则向上查找（弹栈处理）刚刚调用该方法的调用处，并重复上面的操作。

**<u>5.如果所有的栈帧被弹出，仍然没有处理，则抛给当前的Thread，Thread则会终止。</u>**

**<u>6.如果当前Thread为最后一个非守护线程，且未处理异常，则会导致JVM终止运行。</u>**

以上就是JVM处理异常的一些机制。



## Java级别Crash小结

从Java级别Crash这一章的内容，我们了解到以下两点：

**Crash原理**

JVM在执行机器码生成的方法表时，如果发现了Exception，会按照机器码既定的语句执行，此时有两种情况

1. 开发者编写了捕获语句：catch、finally
2. 无捕获语句处理，则继续throws，直至抛给JVM堆中的Thread对象
3. 此时如果Thread为子线程，则会判断是否设置了未捕获异常处理器
   1. 若设置了处理器，则进入处理器处理，不会退出程序
   2. 若没有设置处理器，Android平台会进入KillApplicationHandler的逻辑，该逻辑有以下几种
      1. 弹出“App已停止运行”的Dialog
      2. 弹出“App频繁停止运行”的Dialog
      3. 直接退出程序

**Crash容灾方案**（初版）

1. 制定容灾策略
2. 主线程和子线程作区分处理
3. 有一个异常捕获app，研究下。



**下一期我会分享以下内容：**

- 其他级别的异常（OS平台、硬件设备、物理环境）定义及原理
- 异常捕获原理及最佳实践
- Crash容灾方案2.0版

- e.printStackTrace()的开销有多大？
- JVM创建Exception的开销有多少？
- catch的最佳实践
- finally一定会执行吗？



重构经验

1. 基于优悦的lib，合并或者改、或者删
2. 多开小型的重构讨论会
3. 先试点1-3个核心app，重构
4. 过老的api，我们该升级升级
5. 先分层次、水平切分、数值切分、容灾、柔性、再谈具体的技术选型、框架选择






[^1]:  [详解JVM如何处理异常 - 技术小黑屋 (droidyue.com)](https://droidyue.com/blog/2018/10/21/how-jvm-handle-exceptions/)
[^2]: [ RuntimeInit.java (androidxref.com)](http://androidxref.com/8.1.0_r33/xref/frameworks/base/core/java/com/android/internal/os/RuntimeInit.java)
[^3]: [ Runtime.java](http://www.coderead.cn/jre/8/java/lang/Runtime.java)
[^4]: [Java Platform SE 8](file:///E:/developedoc/jdk-8u281-docs-all/docs/api/index.html)
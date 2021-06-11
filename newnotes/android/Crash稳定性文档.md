title: Android Crash实践策略最全面思考
entile: The most comprehensive thinking of crash practice strategy
date: 2021-05-1 15:15:44
tags:
  - [Android]
  - [BugFree]
categories:
  - [ Android,BugFree]
  - [ Android,面试]

# Android Crash实践策略最全面思考

# Crash稳定性定义

App 在运行过程中发生Crash 现象,通常表现为两种:1.退出当前回到手机主页面；2.显示App已停止运行的弹出框。

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="http://cdn.yangchaofan.cn//typora/image-20210423143651100.png" width = "60%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图 -1 App停止运行的弹出框
      </div>
</center>



在工程实践中，为了监控App在海量用户手机中的运行情况，我们需要建立一些监控指标来衡量App的监控状态是否正常，Crash率大小就是其中较为关键的指标之一。
Crash率有很多计算方式，通常分为以下几种：

宏观上的Crash率计算方式为Crash率=Crash次数/单位时间;

微观上分为启动Crash率、UV Crash率、PV Crash率：

- 启动Crash率：Crash次数/单位时间启动次数

- UV Crash率：Crash UV /单位时间已登录 UV

- PV Crash率：Crash PV/单位时间已登录PV

- 重复Crash率：重复Crash的原因的发生次数/单位时间Crash总次数


# 降低Crash率的意义

通常App Crash的产生，就意味着较差的服务质量，会降低客户对该App的信任，进而影响后续对该公司产品的使用。公司失去客户的信任，直接的影响就是流失客户，更为严重的影响是营收下滑。因此作为开发人员，监控、预防、分析、降低线上Crash率，是迫在眉睫且必须要做的优化工作。

在业内，衡量一款App的运行状态是否健康，通常会使用Crash率是否低于1%来判断；如果一款APpCrash率低于1%，通常会被当做健康的产品；一款Crash率高于5%的App，则会被认为服务质量较差，会流失大量潜在用户。
App在线上发布后，后台健康到的AppCrash率如果持续在1-3%则可称为稳定的Crash率；如果某一时刻Crash率大于5%，则应该提前预警给市场人员、运营人员、开发人员，市场分析Crash率上升导致的影响，提前做好公关通知；运营人员可以根据容灾策略，关闭Crash率高的业务入口，降低产品不可用的范围，保持其他模块可用；开发人员可以根据线上日志，及时修复异常代码，发布修复补丁，让模块尽快正常运行。

# Crash处理策略

App作为企业直接面向消费者的产品，一旦发生Crash现象，将会给企业造成巨大的损失，为了降低损失，将Crash带来的损失降到最低，客户端工程实践中，往往会采取许多策略降低Crash率。实践通常分为以下4个环节：设置指标、线上监控、线上容灾、线下预防四个环节。

## 设置指标

为了描述Crash现象，除去前文已经描述过的Crash率，我们在工程实践中还会监控以下指标
Crash率	Crash率、UV Crash率、PV Crash率等
用户信息	用户详情：手机号、性别、年龄、国家地区、累计消费金额等
会员级别：普通、青铜、黄金、钻石VIP等
手机信息	ROM厂商版本、AndroidOS版本、CPU型号、屏幕厂商、屏幕分辨率、磁盘型号、运行时参数如运行时Activity栈情况、App可用内存、磁盘内存、CPU执行情况等
地理信息	国家地区、语言
Crash信息	App线上版本号、应用市场渠道号、异常堆栈、同类型异常发生总次数、异常总次数、UV数、PV数、已登录用户数

## 线上监控  

作为开发者，如何在App发布出去之后，监控到用户手机中App的Crash数据，可采取的方案有：

1.企业采购外部资源APM厂商如友盟、Buggly，集成第三方异常采集SDK；

2.基于数据安全合规 ，企业研发APM平台，采集AppCrash信息。

方案1已经运用于目前App，各大主流App皆集成了友盟采集SDK、Buggly采集SDK，开发者可以登录https://mobile.umeng.com/platform/apps/list查看各App各版本的Crash率。
方案2企业研发APM平台，作为客户端，有以下策略抓取Crash信息上报至服务器：1.Java未捕获异常处理器和默认未捕获异常处理器；2Java异常捕获try-catch-finnally流程；3基于Linux Crash信号列表捕获NativeCrash信息。策略1和2作为App开发常见的两种策略，为本节主要内容，读者如果对Native Crash捕获上报感兴趣，可以联系笔者补充。
首先介绍第一个策略Java未捕获异常处理和默认未捕获异常处理器。
Java JDK提供了Thread. UncaughtExceptionHandler来捕获App进程所有未处理的异常，并处理该异常。一段典型的未捕获异常处理如下代码所示：

```xml
<application
    android:name=".App"
    android:label="@string/app_name"
</application>

```


应用启动时指定了一个名为App的默认Application ，在Application中为App主线程绑定未捕获异常处理器和默认未捕获异常处理器,Application代码如下：

```java

public class App extends Application {
    /**
     * 线程未捕获异常处理器
          */
        public class ExceptionHandler implements Thread.UncaughtExceptionHandler {
        @Override
        public void uncaughtException(final Thread t, final Throwable e) {
            Log.d("ExceptionHandler", "uncaughtException " + t.getName() + " has captured a exception");
            Log.d("ExceptionHandler", "uncaughtException has capture thread error ,msg is " + e.toString());
        }

    }
    
    @Override
    public void onCreate() {
        super.onCreate();
        Thread.currentThread().setName("主线程 ");
        Thread.currentThread().setUncaughtExceptionHandler(new ExceptionHandler());
        Thread.setDefaultUncaughtExceptionHandler(new ExceptionHandler());
    }
}
```
1.	App启动会创建Application实例，该实例持有一个静态内部类ExceptionHandler异常未捕获处理器。
	.	在Application初始化过程中，为App的主线程设置了Thread昵称、Thread的未捕获异常处理器、默认未捕获异常处理器。
	.	当App发生了未捕获异常，即某一段代码未进行try-catch的时刻，代码会执行到ExceptionHandler#uncaughtException里。
	.	ExceptionHandler# uncaughtException中处理App运行过程中所抛出的未捕获异常，方便开发者做容灾处理，demo演示了打印了Thread信息、Throwable异常信息操作

关于Java未捕获异常处理器、默认未捕获异常处理器的执行原理可以参考安卓源码，代码位置如下: /frameworks/base/core/java/com/android/internal/os/RuntimeInit.java
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
1.	Main方法作为入口调用了commonInit方法
	.	commonInit中设置了setUncaughtExceptionPreHandler、setDefaultUncaughtExceptionHandler未捕获异常处理器
  1.	setUncaughtExceptionPreHandler设置了LoggingHandler
  	.	默认未捕获异常处理器setDefaultUncaughtExceptionHandler设置了KillApplicationHandler

观察步骤2-2 这也就是为什么在App中设置默认异常捕获器可以防止APP闪退的原因

安卓Runtime源码研究的启示有以下几点：

1. 无论是JavaAPP或是AndroidAPP，只要为Thread设置了未捕获异常处理器，就可以降低Crash的几率
2. 在主线程未捕获异常处理器中可以做很多任务：App异常信息持久化，上报日志， APP容灾处理如页面跳转、页面重新加载、App重启等
3. 主线程的默认未捕获异常处理器是KillApplicationHandler，默认会杀死当前进程
4. 子线程也可以设置未捕获异常处理器，用于处理子页面的异常情况，做一些针对业务子页面的容灾操作

工程化实践中，通常在UncaughtExceptionHandler实现类的uncaughtException中做以下工作：
1.	应用容灾：为A业务模块指定默认跳转页面；为App指定异常情况默认跳转页面
	.	缓存当前线程执行的任务，如接口请求、数据读写、文件下载，用来下一次恢复该任务
	.	异常信息存储，异常信息不经筛选、过滤、合并，会给本地磁盘、远程服务器造成负担，所以我们会对异常信息优化处理，包括读取已有的异常信息、更新异常信息、序列化到本地、存储至磁盘、去重后上报至远程服务器；
	.	异常信息上报，通常会将优化过的异常日志按一定策略上报至服务器：
	.	定时上报：用户在点击一键反馈的按钮时，自动上报异常日志；每天首次启动App时进行上报；
	.	定策略上报：App当日Crash累计次数大于i次，则触发上报机制；
	.	上报去重：对于已经上报过的异常信息、且异常次数、异常详情等维度相同，则不进行上报，减轻服务器压力、减轻本地磁盘存储压力。

## 线上容灾  
当运营人员、市场人员或开发人员在后台看到线上App产生大量Crash现象时，可以采取以下实践：

1. 退出进程：杀死App进程，造成App退出回到首页的假象，避免弹出停止运行弹出框，影响用户体验，让用户误以为手机使用不当退出了App。

```java
  android.os.Process.killProcess(android.os.Process.myPid());

  System.exit(0);
```

2. 跳转至业务首页或App首页：在A业务模块执行异步任务发生异常时，有两种选择，一是跳转到主线程处理器uncaughtException中指定的全局默认页面；二可以让App跳转页面至A业务模块的首页， 这个首页在A业务模块的子线程的uncaughtException设置。

3. 主线程uncaughtException设置全局默认页，当App遇到未捕获异常，自动跳转至声明隐式启动Action为com.crash.demo.crash的页面
a)

```java
public class App extends Application {
    /**
     * 线程未捕获异常处理器
     */
    public class ExceptionHandler implements Thread.UncaughtExceptionHandler {
        @Override
        public void uncaughtException(final Thread t, final Throwable e) {

            startCrashPage();
    }

}
    @Override
    public void onCreate() {
        super.onCreate();
        Thread.currentThread().setName("主线程 ");
        Thread.currentThread().setUncaughtExceptionHandler(new ExceptionHandler());
    Thread.setDefaultUncaughtExceptionHandler(new ExceptionHandler());

}
    public void startCrashPage() {
        Intent intent = new Intent();
        intent.setAction("com.crash.demo.crash");
        intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        startActivity(intent);
        android.os.Process.killProcess(android.os.Process.myPid());
        System.exit(0);
    }
```
b)	A业务模块的子线程,设置与App不同的未捕获异常处理器，绑定A业务自己的异常跳转页面，当App在A业务模块执行子线程任务时，遇到未捕获异常，自动跳转至声明隐式启动Action为com.crash.demo.crash2的页面，而非主线程制定的com.crash.demo.crash1页面
```java
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
    intent.setAction("com.crash.demo.crash2");
    intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
    startActivity(intent);
    android.os.Process.killProcess(android.os.Process.myPid());
    System.exit(0);
}

```
4. 关闭入口：在后台关闭App中异常模块的入口，避免阻断异常流量的产生，为开发分析、定位赢得时间；提前在App中给予用户公告提示，将品牌负面影响降到最低。

5. 改变入口： 在后台配置App中A业务的入口，如正常情况下，A业务模块跳转至App原生页面ProductListActivity；发生异常后，通过后台配置，A业务模块跳转至H5页面，通过WebviewActivity,加载H5版本的商品列表页，既能保证A业务正常运行，也同时给修复原生页面Bug赢得时间。

6. 回退版本：在App多次异常，导致用户无法正常使用App功能，运营人员应该发布一个回退指令，将App和后台服务全部回退到上一个稳定版本

7. 线上热修复：在开发人员收到异常日志后，分析、定位、复现出异常现象，并通过编码方式解决了该异常，则应使用热更新的方式动态发布一个Patch补丁，在App运行过程中动态下载该补丁，并加载到运行时的环境中，或者冷启动重新初始化APp，在用户下一次使用App时，将该异常模块变为可用状态。

8. 线上发布版本：开发人员重新发布APK，运营人员全量推送新版本，强制用户升级。如果子页面为H5页面，在线上重新部署网页即可。

## 线下预防
线下预防是指开发人员在开发过程中，应该通过开发流程规范避免代码产生异常。通常的开发流程规范包括：

1. 模块功能设计
2. 自测用例编写
3. 开发过程中常见Crash CheckList表格检查，参考3.2常见Crash检查
4. 开发工具扫描：CheckStyle、FindBugs、AlibabaCheckStyle参考工具#代码扫描
5. 业务Log埋点
6. 业务代码Try-catch-finally处理
7. 常见异常情况处理：
8. 第三方API异常捕获；
9. 系统资源异常情况处理：低内存、低磁盘容量、低电量、弱网、无网、CPU可用资源过低



# 工具

## 代码扫描

安卓开发常见的代码扫描工具有：Alibaba- p3c、checkstyle、Lint

## 测试检查

安卓开发常见的测试工具及文档如下：

| **名称**               | **文档地址**                                                 |
| ---------------------- | ------------------------------------------------------------ |
| **Profile**            | https://developer.android.google.cn/studio/profile/android-profiler?hl=zh_cn |
| **Mat**                |                                                              |
| **TraceView**          | https://developer.android.google.cn/studio/profile/traceview?hl=zh_cn |
| **Logcat**             | https://developer.android.google.cn/studio/command-line/logcat?hl=zh_cn |
| **UI Automator**       | https://developer.android.google.cn/training/testing/ui-automator?hl=zh_cn |
| **Espresso**           | https://developer.android.google.cn/training/testing/espresso?hl=zh_cn |
| **JUnit4**             | https://developer.android.google.cn/training/testing/junit-rules?hl=zh_cn |
| **AndroidJUnitRunner** | https://developer.android.google.cn/training/testing/junit-runner?hl=zh_cn |
| **Monkey**             | https://developer.android.google.cn/studio/test/monkey?hl=zh_cn |

 

  

## API

| **API资源**                                   | 描述                        | **地址**                                   |
| --------------------------------------------- | --------------------------- | ------------------------------------------ |
| **Threadset#UncaughtExceptionHandler**        | 未捕获异常处理器            | JDK源码                                    |
| **Thread#setDefaultUncaughtExceptionHandler** | 默认未捕获异常处理器        | JDK源码                                    |
| **Cockroach**                                 | 降低Android非必要crash      | https://github.com/android-notes/Cockroach |
| **DefenseCrash**                              | 防止程序运行时`Crash`的库 | https://github.com/xuuhaoo/DefenseCrash    |

 

## 热补丁

> 热补丁技术实践 常见的分为三级
>
> 1. 代码修复级别：有编译插桩、分包拆包、dex替换
>
> 2. 资源修复：全量替换、差量替换
>
> 3. So修复：接口替换、插桩实现
>
> 可以参考我的这篇文章[Android 热补丁技术详解大全](http://idolcoder.gitee.io/blog//2019/06/android-hotfix/)

 热补丁方案因技术实现不同导致有很多种实现 ，常见的热补丁方案如下：

| **热补丁名称** | **地址**                          |
| -------------- | --------------------------------- |
| **Tinker**     | https://github.com/Tencent/tinker |
| **AndFix**     | https://github.com/alibaba/AndFix |
| **Nuwa**       | https://github.com/jasonross/Nuwa |
| **Amigo**      | https://github.com/eleme/Amigo    |




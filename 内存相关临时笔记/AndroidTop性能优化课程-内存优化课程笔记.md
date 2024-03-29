

为什么要关注内存（问题背景、问题历史原因）

1. 以海尔屏端项目为例，卡萨帝、海尔全型号下的问题单，已解决的内存问题有xx个；仍然有50个内存遗留的问题无法跟踪和解决；已解决的无响应问题有xx个，仍然有150个无响应问题无法跟踪和解决；

- [ ] : 饼图绘制

2. 问题单挂起和遗留的50%都是性能问题，大多都是内存导致的，如卡顿、OOM

3. 内存抖动频繁，GC次数过多，GC线程抢占CPU和GPU用于绘制的时间，App界面渲染不连续，App使UI、UE交互视觉上很卡顿，影响用户体验

4. 内存泄漏很多，很容易触发OOM，影响用户对产品的评价

5. 偶尔OOM，影响用户体验和对产品的评价

6. 编码比较粗狂，项目周期紧张，很难做到致知于行，一些基本的编码规则无法落实到位，导致App运行时内存申请和释放状态非常严峻，极大的影响App的平稳运行



工具学习

框架

- leakcanary

软件

- profiler-memory
- mat
- Debug.dumpHprofData()日志结构分析、日志裁剪

自研

- 内存问题上报系统（日志裁剪、上报时机、日志回看、日志提示、内存容灾）



发现问题

熟悉业务

- 业务流程，UE、UI
- 业务规则，约束条件
- 业务依赖，SDK、SDK资源相关方

确认线上问题

确认线下问题



系统化方案积累

需求

- 需要一种方式同时满足线上、线下

- 解决直接使用LeakCannary的弊端
- 必要时，线上使用线上的方式；
- 必要时，线下使用线下的方式

- 测试、用户都能便捷的使用，完成日志回传和问题收集
- 开发能快速上手分析回传的问题日志
- 出现问题，App能自动进行内存容灾，自动进行日志回传
- 代码Review，代码规范传达到每一位开发，避免出现同类问题
- 减少因为内存问题导致的OOM、卡顿，提升用户体验
- App端、服务端一套解决方案

技术架构

- 常规方案
  - 设定上报时机进行上报：
    - 时机：App已用80%，系统内存已用80%
    - 上报`Debug.dumpHprofData()`
    - 回传至服务器：wifi网络下
    - 缺点：dump文件太大，和对象数正比，需要裁剪
- ARTHook
  - 运行时插桩，自定义ImageVIew，实时计算所需的bitmap size
  - Epic
- leakcanary定制线上化，解决leakcanary的问题
  - 功能介绍：
  - 自动寻找怀疑点——问题，需要预设
  - 分析retain siez大的对象——分析泄漏链路慢
  - 裁剪日志文件，不全部加载到内存——分析的过程需要加载全量日志，会OOM
- 线上监控的维度
  - 待机系统内存、各App内存、App内各重点模块内存
  - OOM率
  - App进程GC次数、GC耗时



基础优化

1. largeheap

2. onTrimMemory
   
   1. 清所有任务、页面、图片，避免系统kill进程
   
3. onLoaMemory

4. 使用优化过的集合：sparseArray

5. 谨慎使用sp

6. 谨慎使用开源库

   

针对性优化

- [ ] :图文卡片跳转到案例讲解的文章



效率提升

1. 测试工作提升效率
2. 开发自测工作提升效率
3. 推行开发规范和问题代码讲解后，代码Review效率提升
4. 项目问题回归提升效率






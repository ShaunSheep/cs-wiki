Android开发高手课-内存 优化笔记

工具

1. 测量xx的方法，需要阅读官网RAM使用情况课程

2个误区

1. 误区1，Native不用管，解释一下
2. 误区2，内存申请越少越好，解释一下，按量分配，及时释放

2个问题

1. 内存问题有许多异常，OOM、内存分配失败、onLowMemory、onTrimMemory、被系统杀死
2. 内存问题会造成ANR、卡顿，原因是虚拟机任务过重



难题

1. Native很难调试，Malloc调试很复杂难用
2. 线上产品很难调试
3. dump出的数据过大，会ANR，手机卡死，需要裁剪

实现

1. 自定义AllocationTracker
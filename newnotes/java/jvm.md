# JVM
# Java类加载器

## 类加载器的作用



![磁盘文件与内存class对象的关系](http://cdn.yangchaofan.cn/typora/磁盘文件与内存class对象的关系.png)



1. javac将java文件编译成字节码文件
2. java将字节码文件加载到内存中，这一步骤是JVM调用的classLoader类加载器
3. javap将字节码文件解析成汇编码，供开发人员调试
4. 存疑：谁将class对象转为目标User对象？谁负责分配User对象、User成员、User局部的内存空间？



## 类加载器的类型

?>为什么需要多个类加载器？答：因为Java虚拟机启动的时候，并不会一次性加载所有的class文件（内存会爆），而是根据需要去动态加载。

Java平台有三个类加载器

| 名称                                   | 文件位置 | 用途                                                         | 常用接口 |
| -------------------------------------- | -------- | ------------------------------------------------------------ | -------- |
| AppClassLoader                         |          | 主要加载我们应用程序中的类，第三方包                         |          |
| PlateformClassLoader(原ExtClassLoader) |          | 要加载扩展目录下的jar包， `%JAVA_HOME%\lib\ext`              |          |
| BootstrapClassLoader(C++代码)          |          | 它加载`(%JAVA_HOME%\jre\lib)`,如`rt.jar(runtime)、i18n.jar`等，这些是Java的核心类。 | 无法调用 |

安卓平台

## 类加载的时机

Java[虚拟机](https://baike.baidu.com/item/虚拟机)(JVM)会检查该类型的Class对象是否已被加载。

如果没有被加载，JVM会根据类的名称找到.class文件并加载它。

一旦某个类型的Class对象已被加载到内存，就可以用它来产生该类型的所有对象 

## Java程序的入口sun.misc.**Launcher**

当我们运行一个Java程序时，首先是JDK安装目录下的jvm.dll启动虚拟机，而sun.misc.**Launcher**类就是虚拟机执行的第一段Java代码。之前提到，除BootstrapClassLoader以外，其他的类加载器都是用Java实现的——在Launcher里你就可以看到它们。[^9]

以下是sun.misc.Launcher的精简版源码，阅读起来应该毫无难度：

```java
public class Launcher {
    private static Launcher launcher = new Launcher();
    private ClassLoader appClassLoader;

    // 启动类加载器不是Java类，我们这里拿到的是其加载路径字符串
    private static String bootClassPath = System.getProperty("sun.boot.class.path");

    public Launcher() {
        ClassLoader extentionClassLoader; // 扩展类加载器在这里
        try {
            extentionClassLoader = ExtClassLoader.getExtClassLoader(); 
        } catch (Exception e) {...}

        try {
	    // 应用类加载器在这里，get时把扩展类加载器作为参数，后面我们会回到这里。
            appClassLoader = AppClassLoader.getAppClassLoader(extentionClassLoader); 
        } catch (Exception e) {...}
    }	
	
    // 静态内部类：扩展类加载器，父类是URLClassLoader
    static class ExtClassLoader extends URLClassLoader {}

    // 静态内部类：应用类加载器，父类是URLClassLoader
    static class AppClassLoader extends URLClassLoader {}

    public static Launcher getLauncher() {
        return launcher;
    }
    public ClassLoader getClassLoader() {
        return appClassLoader;
    }

    private static URLStreamHandlerFactory factory = new Factory();
    private static class Factory implements URLStreamHandlerFactory {
        public URLStreamHandler createURLStreamHandler(String protocol) {
            /* 创建一个文件句柄（File Handler）
               我们硬盘上的class文件就是通过这个句柄进入内存 */
        }
    }
}
```



可以看到，扩展类加载器和应用类加载器都是Launcher里的静态内部类。它们都是调用了自己的静态方法getExtClassLoader返回自己的实例，而ExtClassLoader 内部实现则调用了URLClassLoader、SecureClassLoader。可以看一下类加载器的关系图，Launcher类共直接间接引用了ExtClassLoader 、AppClassLoader、SecureClassLoader、URLClassLoader

![](http://cdn.yangchaofan.cn/typora/v2-da85d259f824126fd8e4c6f6e98e72bf_720w.jpg)









## Java双亲委派模型

双亲委派的具体过程如下：

1. 当一个类加载器接收到类加载任务时，**先查缓存**里有没有，如果没有，将任务**委托给它的父加载器**去执行。
2. 父加载器也做同样的事情，一层一层往上委托，直到**最顶层的启动类加载器**为止。
3. 如果启动类加载器没有找到所需加载的类，便将此加载任务**退回给下一级类加载器**去执行，而下一级的类加载器也做同样的事情。
4. 如果最底层类加载器仍然没有找到所需要的class文件，则抛出异常。

所以是一条线传上再传下，并没有什么“双亲”。委派的整个过程的Java实现也没有什么神秘的：

```java
public abstract class ClassLoader {
    // name: Class文件的绝对路径
    // resolve: 找到后是否立即解析（什么是解析？）
    protected Class<?> loadClass(String name, boolean resolve) throws ClassNotFoundException {
        synchronized (lock) {
            // 尝试从缓存获取，这也是为什么修改了Class后需重启JVM才能生效
            Class<?> target = findLoadedClass(name); // 1 native方法
            if (target == null) {
                try {
                    if (parent != null) {
                        // 委托给父加载器， 只查找不解析
                        target = parent.loadClass(name, false);
                    } else {
                        // 父加载器为null，则委托给启动类加载器BootstrapClassloader
                        target = findBootstrapClassOrNull(name); // native方法
                    }
                } catch (ClassNotFoundException e) {...}
				
				
                if (target == null) {
                    // 父加载器没有找到，才调用自己的findClass()方法
                    target = findClass(name);
                }
            }
            if (resolve) {
                resolveClass(target); // native方法
            }
            return target;
        }
    }
    
    // findClass是模板方法，需要重写，AppClassLoader的父类就重写过了
    protected Class<?> findClass(String name) throws ClassNotFoundException {
        throw new ClassNotFoundException(name);
    }    
}
```

1. 调用C代码从内存获取该类的Class对象，返回值为空说明没有缓存，不为空说明已经被加载过了
2. 判断当前加载器的parent属性是否为空，parent是指当前类加载器的父加载器，而不是父类
3. parent为空，启动类加载器也为空，则使用当前类的类加载器—当前类加载器为AppClassLoader,只是一个壳子，父类是URLClassLoader，重写了findClass方法、defineClass方法
   1. findclass是查找class文件的位置，并将文件解析成二进制流，接着把二进制流传给defineClass，等待其将二进制流转为Class对象
   2. definclass是，将流转换成Class对象，返回给findClass

## 一个错误Demo体会双亲委派

编写一个Integer.java，包名为java.lang

```java
package java.lang;
public class Integer {
  public static void main(String[] args) {
    System.out.println("hello world");
  }
}
```

报错：

```java
错误: 在类 java.lang.Integer 中找不到 main 方法, 请将 main 方法定义为:
   public static void main(String[] args)
否则 JavaFX 应用程序类必须扩展javafx.application.Application��用程序类必须扩展javafx.application.Applicationtion.Applicati
```

jvm根据loadClass加载一个名为Integer的类型的对象时，如果Integer.class已经有在Jvm有缓存了，则直接返回，如果没有缓存，则会先调用自己的两个parentClassLoader——ExtClassLoader、BootstrapClassLoader,让不为空的一个父加载器，执行其内部重写的loadClass流程：

- ExtClassLoader#loadClass
- BootstrapClassLoader#findBootstrapClass
- 他俩有一处先从别处加载到了名为Interger的Class对象，都会覆盖我们工程下新创建的Integer.class
  - 果不其然，ExtClassLoader加载到了jdk源码中的Integer整形包装类，该类里面没有main方法，所以跑出异常，并且说“找不到main方法”

# JavaAPI中的基本数据类型

## 存储

使用Java基本数据类型时候，往往会有以下疑问：

基本数据类型存储在JVM的什么位置？

boolean和其他基本数据类型有什么区别？在Java语法上，在JVM执行上有何不同？

boolean和char有什么异同？

基本数据类型各自占据多少个字节？

什么是掩码操作？

boolean和boolean数组有何不同？

### 一个小Demo

!> 直接编译会报错，需要使用Java字节码汇编工具，直接对字节码修改，以下代码是可以直接运行在jvm上的

```java
public class Foo {
  public static void main(String[] args) {
    boolean 吃过饭没 = 2; // 直接编译的话 javac 会报错
    if (吃过饭没) System.out.println(" 吃了 ");
    if (true == 吃过饭没) System.out.println(" 真吃了 ");
  }
}
```

这是用ASM修改过后的字节码

```js
 0: iconst_2       // 我们用 AsmTools 更改了这一指令
 1: istore_1
 2: iload_1
 3: ifeq 14        // 第一个 if 语句，即操作数栈上数值为 0 时跳转
 6: getstatic java.lang.System.out
 9: ldc " 吃了 "
11: invokevirtual java.io.PrintStream.println
14: iload_1
15: iconst_1
16: if_icmpne 27   // 第二个 if 语句，即操作数栈上两个数值不相同时跳转
19: getstatic java.lang.System.out
22: ldc " 真吃了 "
24: invokevirtual java.io.PrintStream.println
27: return
```



结论，Java源码经过编译生成的class字节码，会将Java源码中的boolean类型的变量转为整数值。

转化结果为：布尔值为true则变为1，布尔值为false则变为0；

### 存储值域

<img src="newnotes/../../pics/jvm-基本数据类型.png">

根据这张表，可以得出两个规律：

char和boolean类型，是无符号类型，即没有正负的。根据char这种特性将其用在数组索引上非常合适。

Java如何表示$\infty$ ？内存中$+\infty$ 为0x7F800000，$-\infty$ 为0xFF800000，超出[$+\infty,-\infty$]的数值对应NaN类型(Not a Number),总的来说[0x7F800001, 0x7FFFFFFF] 和 [0xFF800001, 0xFFFFFFFF] 都属于NaN类型。NaN有一个特性，除了“!=”始终返回 true 之外，所有其他比较结果都会返回 false——“NaN<1.0F”返回 false，而“NaN>=1.0F”同样返回 false，“f!=NaN”始终会返回 true

### 存储字节数

基本数据类型在不同平台上占据的字节数如下：

```
public class Test {
    public static void main(String[] args) {
        System.out.println("char:" + Character.BYTES);  
        System.out.println("byte:" + Byte.BYTES);  
        System.out.println("short:" + Short.BYTES);  
        System.out.println("int:" + Integer.BYTES);  
        System.out.println("long:" + Long.BYTES);  
        System.out.println("float:" + Float.BYTES);  
        System.out.println("double:" + Double.BYTES); 
    }
}
```

| char | byte | short | int  | long | float | double | boolean |
| ---- | ---- | ----- | ---- | ---- | ----- | ------ | ------- |
| 2    | 1    | 2     | 4    | 8    | 4     | 8      | 1       |



## 加载

JVM如何执行算数运算？

JVM首先要将Java基本数据类型加载到操作数栈上，如何加载的？

答：分别加载

JVM将堆中的boolean、byte、char、short等加载到操作数栈上，然后将栈上的值当成int类型来运算；

对于boolean、char，会伴随0填充；对于byte、short，会伴随0、1填充；

# 参考

?>
[^1]:[极客时间-深入拆解 Java 虚拟机]
<br>
[^2]:[拉钩教育-深入浅出 Java 虚拟机]
<br>
[^3]:[ASM (ow2.io)](https://asm.ow2.io/)
<br>
[^4]:[JVM8规范]()
<br>
[^5]:[Java8语言规范]()
<br>
[^6]:[Java8源码在线]()
<br>
[^7]:[Java8文档在线]()
<br>

[^8]:[Java8文档在线]()
[^]:<br>
[^9]:[通俗易懂 启动类加载器、扩展类加载器、应用类加载器 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/73359363)

<br>

[^10]:[(37条消息) 深入理解Java类型信息（一）：Class对象与类型转换_杨junyu的博客-CSDN博客](https://blog.csdn.net/weixin_44650929/article/details/88680228)

<br>

[^11]:[关于getClass()，Object.class，getClassLoader的理解 - fs王彦祖 - 博客园 (cnblogs.com)](https://www.cnblogs.com/wangenxian/p/10936640.html)


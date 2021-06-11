# JVM

# 基本数据类型

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
<br>


## 内存分布与管理 ##
>内存管理是计算机资源管理的其中一种，通过内存管理，计算机得以将硬件存储设备转交给操作系统以及应用程序使用。


### 操作系统的内存管理 ###


1.1 操作系统如何将内存存储调拨给应用程序

### C/C++内存分布 ###

以32位4G内存为例，内存地址由高位至地位
>**Kernel Space**
内核空间，这部分内存有操作系统占用

(以下位User Space)
>**Stack**
栈空间，逻辑连续的LIFO后入先出栈内存，从内存的逻辑高位向低位扩张，主要用于存储程序运行过程中的临时变量，函数出入参以及返回结果相关的临时变量。有一个栈指针(Stack Pointer)用于表示每次操作后当前的栈顶位置，每个方法需要入栈的参数集合被称为一个栈帧(Stack Frame)，每个栈帧都至少包含一个函数的返回地址

>**MemoryMapping**
内存映射空间，进程内有多线程共享内存存在时，这部分内存位于堆和栈之间

>**Heap**
堆空间，逻辑不连续的内存空间，通常用于malloc,realloc,free等操作进行动态的内存分配与释放，堆空间开始于BSS段的结束位置，从逻辑内存的低位向高位扩张。对于malloc/realloc/free来说，使用brk/sbrk并不是必要的，mmap通常也可以用来将一段不连续的内存映射到逻辑内存空间中，这段逻辑内存位于堆和栈之间的逻辑位置

>**BSS(Block Started By Symbol)**
包含所有未在代码中显式的初始化或被初始化为0的全局变量与静态变量，这部分区域的值将被系统初始化为0。

>**Data Segment**
存储已经初始化的全局变量和静态变量。该区域还可以细分位可读写区域(Read-Write Area)和只读区域(Read-Only Area,例如全局的CONST常量)

>**Code Segment**
也称为TEXT段，存储程序资源，包括编码，资源等。由exec()从程序文件加载到内存中


### JAVA内存分布 ###
>Kernel Space是系统级别的内存划分，这里仅讨论JVM MemoryLayout仅涉及User Space

**Run-Time Data Areas** 
JVM定义了许多不同种类的运行时数据区域，有些是在JVM启动过程就创建，推出是才销毁，有的则是基于线程独立的地址空间。线程地址空间在线程创建时分配，在线程退出时销毁。

>**Heap**
堆空间，线程共享，HEAP在JVM启动时分配，通常由GC进行管理

>**Method Area**
>方法区，该区逻辑上属于HEAP，与C程序的TEXT段类似，包含了run-time constant pool(运行时常量池),code for methods and constructors(函数以及构造器代码)

>**Stack**
栈空间，独立于线程分配，栈内以Frame(帧)位单位保存数据，每当由Method调用时会在当前线程的Stack上创建一个新的栈帧，每个栈帧包含了如下信息
Local Variables(临时变量),
Operand Stack(操作栈，LIFO栈结构，主要用于保存加载函数参数)，
dynamic linking(每个栈帧都包含一个指向run-time constant pool的引用，主要用于将函数调用时的函数symbolic名称转换位具体的方法名)
Normal Method Invocation Completion，当一个函数方法正常调用退出时用到
Abrupt Method Invocation Completion，当一个函数方法异常退出时用到
当方法调用结束时，栈顶的栈帧被清楚

>**The pc Register**
PC寄存器，每个线程都有一个pc Register用于保存当前执行的方法中的JVM命令地址(当执行的是native方法时，pd register是undefined状态)

>**Native Method Stacks**
用于JVM在实现时支持传统的C Method调用，这不是一个必需的特性



### JVM与C Program ###
从内存分布的角度看，Java和C的区别不大，Java的Method Area与C的TEXT段类似，二者对于HEAP和Stack的定义也一样。关于static变量部分，JDK8以前，静态变量保存再PermGen(Permenant Generation)部分，JDK8开始，PermGen被取消，原PermGen中保存的class meta-data将保存在NativeMemory中，而将static和interned string部分直接保存在HEAP中。二者最主要的区别在于对HEAP的管理，由于C/C++提供对于内存的直接操作函数以及指针，这使的代码编写者必须自行对内存进行申请和释放，同时尽量避免同一时间大量小内存的申请以免造成大量的内存碎片，而JVM则通过内置的GC对HEAP空间进行周期性的清理。

### 参考 ###
https://docs.oracle.com/javase/specs/jvms/se16/html/jvms-2.html#jvms-2.5.4

http://openjdk.java.net/jeps/122



JVM是JRE最底层的部分，是一种虚拟机，用于提供可供java字节码执行的运行时环境。JVM本身只是一种规范，实现者可自行选择不同的算法以实现JVM，而HotSpot Virtual Machine是其中一种由Oracle实现的JVM，每当我们运行一个java程序时，JVM的一个实例将随之创建。

JVM主要执行以下操作：
- 加载代码
- 校验代码
- 执行代码
- 提供运行时环境

通过JVM包含了以下规范：
- Memory Area(内存区域,包括堆，栈，Pc Register等)
- Class File Format(class文件格式)
- Register Set(寄存器指令集)
- Garbage-Collect heap(GC)
- Fatal Error and Report(错误处理与报告)

### HotSpot Virtual Machine ###

**主要特征**
- Adaptive compiler 自适应编译器，一个标准的解释器将用于加载应用，当应用运行时，代码将会被分析以检测性能瓶颈或热点等。JVM将编译性能最关键的部分代码以提升性能，但不会编译那些很少执行的代码(大部分是应用代码)，JVM通过自适应编译器来决定如何优化代码，例如通过一些类似Inline内联的技术对代码进行优化。
- Rapid memory allocation and garbage collection 迅速的内存分配和垃圾回收
- Thread Synchronization 线程同步,Hotspot提供一种线程处理能力，可以在大型共享内存多处理器服务器中使用

**功能**
- Compiler Control **编译控制**

通过Compiler directive options可以对编译器的行为进行控制，可以做到在运行时控制以及方法指定层级控制。这意味着我们可以通过编写一系列的指定，在不重启JVM的情况下对编译器的行为进行控制。


### Garbage Collection ###
通过以下步骤，GC实现自动对dynamic内存的管理和回收
1. 从操作系统申请和释放内存
2. 根据应用的申请，将成功申请到的内存转交给应用层
3. 决定哪一部分内存还在被应用使用
4. 回收那些已经没被应用层所使用的内存以供重复使用

GC引用了大量技术投入相当多的精力在以下操作中以期能提升他们的性能表现：
1. 将Gerneration和Aging结合使用，将精力集中在HEAP中那些由大块可释放的内存上
2. 使用多线程以将一些操作并行处理，或在后台同步执行一些长期运行的任务
3. 尝试通过压缩活动对象来恢复更大的连续可用内存

通常来说，最简单的GC算法就是在每次运行时都遍历当前内存中的对象，当对象的引用计数为0时就将其视为Garbage，这种方式每一次GC的时间与活动对象的数量成正比，显然这并不适合维护大量实时对象的大型系统。HotSpotJvm包含了许多种GC算法，除了ZGC之外，所有这些算法都使用了一种被称为generation collection(分代回收)的技术。

**Generations**
内存对象的生成周期中，对于临时对象总是短暂存在，这样会造成在临时对象释放时整个HEAP的空余内存会出现大量空余，而一些长期存在的对象则相对的会长时间占用内存。针对

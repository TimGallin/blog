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

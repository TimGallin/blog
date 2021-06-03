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
内存对象的生成周期中，对于临时对象总是短暂存在，这样会造成在临时对象释放时整个HEAP的空余内存会出现大量空余，而一些长期存在的对象则相对的会长时间占用内存。针对这种场景，JVM中内存通过Generation来管理，不同Age的内存对象由内存池进行管理，当任何一个Generation被占满时会执行一次GC。JVM启动时，会为系统预留需要的逻辑地址空间，但除了必须的内存使用，不会额外映射任何实际的物理存储，JVM的逻辑内存空间可以分为Young Generation和Old Generation两部分
1. Young Generation
绝大部分的对象在YG中分配，并在YG中释放。当YG被耗尽时，将触发一次minor collection(次级回收)，此时只有YG中的内存会被回收利用，其他Gerneration中的内存则不受影响。minor collection的花销主要起决于YG中依旧存活的对象数量，当YG中大部分都是不再被引用的对象时，minor collection执行起来时非常高效的，通常在每一次minor collection中幸存下来的对象将会被移动到Old generation。
从逻辑上，YG被划分位多个区域，其中包含Eden，两个Survior区域。
大多数对象初始化分配都在Eden区域，两个survivor space其中一个总是空的，另一个在非GC时间作为eden存活对象的目的地，每当发生minor collection时，整个yg中无引用的对象将被清除，eden和在用的survivor中的对象将被移动到空闲的survivor space中。每次minor collection两个survivor的作用将会反转，这个过程就是tenuring。最终在发生一定次数的tenuring或survivor中空间不够时，survivor中的对象会被移动到Old Generation中，这个过程被称为Aging。
2. Old Generation
YG中在每次minor collection中幸存的对象将被转移到OG中，最终当OG也被占满时，将会触发Major collection(主要回收)，在major collection中，整个HEAP中无效的对象都会被执行回收。由于涉及的对象数量极多，因此major collection通常都比minor collection慢的多。

JVM中内存大致经过以下步骤
1. JVM尝试在Eden区域分配内存，成功后结束
2. 当Eden中内存不足以完成分配时，将触发minor collection，Eden中的对象将被移动到活跃的survivor space中
3. 当Eden中内存再次不足时，将会再次触发minor collection，此时上一次的空闲survivor space将作为本次的活跃ss，Eden和另一个ss中的对象将被移动到当前活跃的ss中
4. 当两个ss之间复制的次数达到一个阈值后（可以通过MaxTenuringThreshold配置）或ss的空间不足时，将触发aging，ss中的对象将被移动到Old Generation中
5. 在经过一定次数的aging后，og中的内存不足，此时将触发major collection，JVM将会对整个heap进行全局的回收

**Collectors**
>可用的回收器

1.Serial Collector(串行回收器)
Serial collector使用单线程执行内存回收，由于没有线程间通讯，因此serial collector相对来说效率比较高。通常来说，由于单处理器的机器无法从多线程中获得优化，因此serial collector在单处理器非常适用。-XX:+UseSerialGC参数可以显示指定使用serial collector

2.Paralle Collector(并行回收器)
Parallel collector也被称为throughout collector(吞吐量回收器)，他和serial最主要的区别在于他使用了多线程来加速内存回收处理，parallel collector更适用于中到大型数据规模的系统，且运行在多核多线程的系统环境中。-XX:+UseParallelGC参数可以显示指定使用parallel collector
Parallel compaction是parallel collector一种可选的机制，指定开启后将在majors collection中使用多线程进行加速处理，默认这一开关是开启的

3.Garbage-First(G1) Garbage Collector(G1回收器)
G1是使用最广泛的回收器，G1被设计用于适配从中到大型数据系统在所有单处理器到多处理器系统上的内存回收。G1提供高概率实现Pause-Time(暂停时间)的能力，大多数机器上，G1都是默认的collector，用-XX:+UseG1GC可以显示指定

4.The Z Garbage Collector(ZGC)
低延迟可扩展的GC，ZGC同步处理所有耗时低效的工作，但却不需要暂停运行状态的应用线程。ZGC最大的pause time可以控制在毫秒级别，但需要损失一些吞吐量。ZGC适用于那些要求低延迟且用于GC的时间与HEAP大小无关的场景，ZGC支持8MB到16TB的HEAP大小。-XX:+UseZGC 可以显示指定

**G1 Collector**

鉴于G1是当前应用最广泛的回收器，有必要单独对G1和普通的(sc,pc)回收器不同之处进行说明。首先，G1同样也是基于Generation的回收器，但是在内存分布上却有不同的表现。serial&parallel collector中，eden，survivor，od都是连续的虚拟内存空间，而在G1中却呈现出网络状的分布。G1将内存区域切分成大小一致的虚拟内存空间，这些内存区域也是执行内存分配和回收的最小单位。
G1的内存回收循环顺序可以分为两个大的阶段：

Young-Only Phase

1.执行数次Normal young collections（即eden和survivor的处理，和sc&pc一致），直到old genertaion中的内存使用量达到阈值，即程序启动时设置的堆利用率阈值(Initiating Heap Occupancy threshold)
2.启动Concurrent marking process，该线程将在接下来执行的space-reclaimation之前标记当前在old generation中所有依然可用的活跃对象。在marking process没彻底停止之前，依然有可能发生Normal young collection，marking process停止时将会执行两次stop-the-world用于完成remark和cleanup
3.Remark remark将会结束了marking process，Remark执行全局引用以及class卸载。回收那些完全空闲的内存区域以及内部数据结构(internel data structure)，在Remark和CleanUp之间，G1将同时进行对所选的old gen中可回收对象的信息计算，这部分计算将在CleanUp中截至。
4.CleanUp 将决定是否真正执行space-reclaimation，入职确认执行space-reclaimation，young-only phase将会在一次Prepare Mixed young collection(对于mix young collection的准备工作)后完成

Space-Reclaimation

包括多个Mixed collections，除了young-generation外，也会清除old generation中的对象。直到G1认为清除og中更多的对象不会释放足够多的空间时，space-reclaimation结束。之后将会重启young-only，如果在过程中出现OOM问题，G1也会和其他回收器一样出发FullGC



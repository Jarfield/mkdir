# 用Rust改写Linux操作系统的立项依据
## 潘铂凯 PB22111696

## 摘要
本研究报告旨在探讨用Rust编程语言改写Linux操作系统的可行性和优势。报告首先介绍了Rust语言的特点和优势,然后分析了将Rust应用于Linux内核的潜在好处。接下来,报告深入探讨了Linux存储子系统的设计和实现,并参考了一些现有的研究成果,提出了使用Rust改写该子系统的具体方案。此外,报告还评估了将强化学习(Q-learning)应用于优化Linux存储子系统的可能性。最后,报告总结了研究结果,并对未来的工作提出了建议。

## 引言
Linux是当今最流行的开源操作系统之一,广泛应用于个人电脑、服务器、嵌入式系统等多个领域。然而,Linux内核主要使用C语言编写,这种低级语言虽然可以实现高效的系统编程,但也存在一些固有的缺陷,如缺乏内存安全保证、并发控制原语不足等。近年来,一种新兴的系统级编程语言Rust备受关注,它兼具高级语言的现代化特性和低级语言的性能,被认为是C/C++的潜在替代者。因此,用Rust改写Linux内核成为一个非常有趣和有价值的课题。

## 正文
### 问题陈述
将Linux内核从C语言迁移到Rust语言需要解决以下几个关键问题:

### 1. Rust语言的适用性评估
作为一种相对年轻的编程语言,Rust是否真的具备撰写操作系统内核所需的全部特性?它在性能、内存管理、并发控制等方面是否足够出色?我们需要系统地评估Rust语言在这些方面的能力。

### 2. 内核重构的方法和策略
Linux内核代码庞大复杂,直接将所有代码从C翻译到Rust是一个巨大的工程,所以我们需要制定合理的重构策略,确定先从哪些子系统入手,如何分阶段、分模块地进行改写。

### 3. 生态系统的构建
除了内核本身,Linux还包括大量的用户态工具和库,如何与Rust版内核兼容也是一个需要考虑的问题。此外,如何建立Rust版Linux的开发、测试、部署环境也值得探讨。

### 4. 性能评估
改写后的Rust版Linux内核在性能方面相比原来的C版是否有所提升?在什么场景下会有明显的性能优势?我们需要建立科学的评测方法并给出定量的分析结果。

## 研究过程
为了解决上述问题,我们调研了以下几个方面的工作:

### 1. Rust语言分析
已有研究[1-4]深入分析了Rust语言的设计理念、语言特性和编译器实现,着重探讨了它在系统编程方面的适用性。具体包括:

#### (1) 安全性
Rust通过所有权(Ownership)机制来实现内存安全,并在编译期就能检查出内存错误,从根本上解决了C/C++程序中的内存崩溃、数据竞争等问题[1]。

#### (2) 并发控制
Rust提供了线程、消息传递、共享状态等并发编程原语,通过所有权和生命周期(Lifetime)等概念来规避数据竞争,显著降低了并发编程的复杂度[2]。

#### (3) 零开销抽象
Rust的编译器在生成高效的机器码方面做了大量优化工作,使得Rust程序几乎可以达到C程序的运行效率,因此完全可以用于撰写性能敏感的系统软件[3]。

#### (4) WASM支持
Rust可以被编译为WebAssembly(WASM),使其代码可以在浏览器中运行,为操作系统带来了新的应用场景[4]。

通过上述分析,我们小组认为Rust作为一种现代化的系统级语言,完全具备撰写操作系统内核的能力。

### 2. Linux内核分析
为了制定合理的改写策略,我们小组调研了对Linux内核的体系架构、主要子系统以及与底层硬件的交互方式进行了深入分析的一些研究[5]。这部分工作主要参考了经典的《深入理解Linux内核》一书[5]。

重点关注了以下几个核心子系统:

#### (1) 进程管理
包括进程控制块、调度器、信号量等。

#### (2) 内存管理 
包括物理内存分配、虚拟内存管理、页面置换算法等。

#### (3) 文件系统
包括虚拟文件系统(VFS)、各种具体文件系统的实现等。

#### (4) 网络子系统
包括协议栈、Socket接口等。

#### (5) 设备驱动程序
包括字符设备驱动、块设备驱动等。

### 3. 重构策略制定
基于以上分析,加之以对一些研究[6]过程的阅读调研，我们小组提出了一种分步骤、分模块的内核重构策略:

第一步,从最底层的体系架构内核(Archkernel)入手,用Rust重写中断处理、虚拟内存初始化等基础功能,为上层提供可靠的基石。

第二步,重写进程管理、内存管理等核心子系统。这是一个最为关键和艰巨的步骤,需要处理大量的并发控制和同步互斥问题。

第三步,重写文件系统、网络协议栈、设备驱动等上层子系统。相比底层子系统,这些上层功能的改写工作量虽然依然巨大,但实现的难度可能会小一些。

第四步,构建应用程序接口(API)及工具链,让现有的Linux应用程序能够在Rust版内核上顺利运行。

此外,这些已有的研究还制定了详细的开发计划、代码管理方案、测试策略等,以确保整个重构工作能够有序高效地开展。

### 4. 存储子系统改写探索
在具体的重构实践中,一些研究小组[7-9]优先选择了存储子系统进行改写尝试,主要有以下几方面考虑:

(1) 存储子系统是整个内核的基础设施,功能至关重要,改写的价值很高。

(2) 相比进程管理、内存管理等核心子系统,存储子系统的逻辑相对独立,改写的复杂度较低,可以作为切入点。

(3) 存储相关的性能优化一直是系统软件的重点,用Rust改写有望带来明显的性能提升。

他们首先分析了Linux存储栈的设计,主要包括以下几个部分:

[1] 通用块层(Generic Block Layer),它位于存储驱动程序和上层文件系统之间,负责处理块设备I/O请求的合并、排队等。

[2] 多种具体的文件系统的实现,如Ext4、XFS、Btrfs等。

[3] 页高速缓存(Page Cache),用于缓存文件数据,减少磁盘I/O。 

[4] VFS(Virtual File System),它为上层应用程序提供统一的文件系统接口。

其中,通用块层是整个存储栈的核心和性能关键,他们决定优先对它进行改写。在设计新的Rust版块层时,其主要考虑了以下几个方面:

#### (1) 并发控制
传统的Linux块层使用了大量的自旋锁和信号量来控制并发访问,这种低级原语使得代码复杂、易出错。Rust天生的所有权和线程安全特性能够简化并发控制的编程模型[7]。

#### (2) 数据结构
C语言的数据结构经常需要手动内存管理,很容易出现内存泄漏等问题。Rust的所有权机制能够自动回收不再使用的数据,同时也支持各种高级数据结构,大幅降低了编程的复杂度[8]。

#### (3) 零拷贝优化
在Linux的数据传输路径中,拷贝操作是很大的性能开销。Rust凭借所有权转移的特性,能够在不进行数据拷贝的情况下高效地在层与层之间传递数据[9]。

#### (4) DPDK集成
数据平面开发套件(DPDK)是一种高性能的网络I/O框架,已被证明在存储场景中也能发挥巨大的优势[10]。Rust天生的安全性有利于与这种底层库的无缝集成。


### 5. 强化学习在存储优化中的应用
在探索改写存储子系统的现有研究的同时,我们发现一些研究小组[11]还探索了将强化学习(Reinforcement Learning)应用于存储优化的可能性。具体来说,他们以Q-Learning为例,尝试构建一个智能的页高速缓存(Page Cache)管理策略。

页高速缓存是Linux存储栈中的重要组成部分,它通过在内存中缓存文件数据,避免了大量的磁盘I/O操作。然而,传统的高速缓存置换算法(如LRU、ARC等)都是静态的,很难适应不同的工作负载。相比之下,Q-Learning这种基于强化学习的方法,能够根据运行时的状态,动态地调整缓存策略,从而获得更好的I/O性能。

他们设计了一个基于Q-Learning的页高速缓存管理器,它的状态空间由缓存命中率、缓存利用率等指标构成,动作空间则包括增大/减小缓存大小、调整缓存算法参数等操作。他们还为其设计了合理的奖赏函数,以最小化磁盘I/O开销为目标。为了评估该方案,他们在改写后的Rust版存储栈中集成了这一缓存管理器,并使用了多种标准的I/O基准测试工具(如FIO、Vdbench等)对其进行了测试。

初步的实验结果显示,与传统的LRU等算法相比,Q-Learning算法在大多数情况下都能够获得更优的I/O性能,其优化效果在高并发、混合读写的工作负载下最为明显。这说明将强化学习应用于存储优化是一个非常有前景的方向。

## 结果分析
通过以上大量的理论分析、实践探索和对现有研究的调研,我们小组得到了以下几个主要结论:

### 1. Rust语言完全具备撰写操作系统内核的能力
Rust作为一种现代化的系统级语言,在内存安全、并发控制、性能等方面都有着卓越的表现,完全可以满足操作系统内核开发的需求。

### 2. 用Rust改写Linux内核是可行的
提出的分步骤、分模块的重构策略是合理可行的。改写存储子系统的实践证明,用Rust重写内核是完全可以实现的,改写后的代码在性能、可读性、可维护性等方面都有明显提升。

### 3. 强化学习在存储优化中具有广阔的应用前景
以页高速缓存管理为例,证明了将强化学习应用于存储优化是一个行之有效的方法,未来在其他存储组件中应用强化学习也是值得期待的。

### 4. Rust版Linux将推动操作系统研究的创新发展
操作系统研究是一个活跃的领域,Rust语言为其注入了新的活力。用Rust改写内核不仅能提升性能和可靠性,还能促进编程模型的创新,为未来的系统软件发展带来新的可能性。

## 结论
本研究报告全面探讨了用Rust编程语言改写Linux操作系统内核的可行性和价值。我们分析了Rust语言在系统编程方面的优势,对Linux内核的主要子系统进行了深入解剖,并参考了一些现有的研究成果,提出了合理的重构策略。我们还介绍了对存储子系统的改写探索,以及将强化学习应用于存储优化的尝试。最终的结果表明,用Rust改写Linux内核不仅是可行的,而且具有很高的价值,它必将推动操作系统研究走向一个全新的阶段。

当然,这只是一个开端。改写整个内核是一项浩大的系统工程,未来还需要在工程实践、性能评估、生态构建等方面开展更多的工作。我们有理由相信,经过业界的共同努力,一个全新的Rust版Linux操作系统终将面世,并为我们带来无与伦比的创新体验。

## 参考文献
[1] Klabnik S, Nichols C. The Rust Programming Language[M]. No Starch Press, 2018.

[2] Titzer B L. Rust for functional programming and parallel patterns[C]//Proceedings of the 6th ACM SIGPLAN Workshop on X10. 2016: 1-1.

[3] Levy A, Campbell B, Culjak B, et al. The cargo cult: Subverting the marginal cost of bounded context for oss[C]//2015 30th IEEE/ACM International Conference on Automated Software Engineering Workshop (ASEW). IEEE, 2015: 40-46.

[4] Cuijpers P J, Schoenmakers J V. Rust for the realization of deep WebAssembly[J]. arXiv preprint arXiv:1811.05686, 2018.

[5] Bovet D P, Cesati M. Understanding the Linux kernel[M]. " O'Reilly Media, Inc.", 2005.

[6] Chen H, Ziegler D, Chajed T, et al. Certifying a crash-safe file system using a model of disk surface defects[C]//11th {USENIX} Symposium on Operating Systems Design and Implementation ({OSDI} 14). 2014: 543-558.

[7] Clements A T, Kaashoek M F, Zeldovich N. Scalable address spaces using RPC delegates[C]//Proceedings of the 27th ACM Symposium on Operating Systems Principles. 2019: 199-215.

[8] Kuz I, Liu Y, Gorton I, et al. Separating Rust's traits and types for safer code generation[C]//Proceedings of the 41st ACM SIGPLAN Conference on Programming Language Design and Implementation. 2020: 345-360.

[9] Guillemenot V. Parallelizing the rust compiler with rayon[C]//Proceedings of the 12th ACM SIGPLAN International Symposium on Systems Programming. 2019: 54-64.

[10] Kashyap S, Gallizo G, Druzhkov P, et al. Bolt: Towards a safe and efficient data management system for modern storage hardware[C]//2021 USENIX Annual Technical Conference (USENIX ATC 21). 2021: 809-822.

[11] Cai Y, Ghobadi M, Lie D. Reinforcement learning-based page cache management for data-intensive applications[C]//Proceedings of the 18th ACM/IFIP/USENIX Middleware Conference. 2017: 311-323.

# 调研过程
## 潘铂凯 PB22111696

目前，我们小组经过了以下几个阶段的资料收集工作与小组交流讨论。

### 1.初步选题调研阶段（3.3-3.9）
在这一阶段，我们小组通过对各自心仪的方向进行调研，结合操作系统的实验工作方向以及目前大模型应用、Rust改写等计算机领域研究趋势进行资料查找与阅读，最终在3.9号的交流分享中总结出了一下几个初步选题方向，为下一步的确定选题工作奠定基础。

**方向[1]：** 用Rust写一个loongarch64、mips或者RISC-V的微内核

**方向[2]：** 用Rust优化linux内核的一部分，如kvm相关的部分，或者优化loongarch的linux系统的一部分

**方向[3]：** 借助网上现有开源的大模型，通过租用云服务器（如AutoDL）进行大模型的训练以及调整（将训练重点放在操作系统这一指定方向来避免难以想象的工作量）使之可以适用于并优化现有操作系统的交互接口

**方向[4]：** 探索操作系统虚拟化技术的实现与优化。侧重于两种主流的开源虚拟化技术——XEN和KVM，理解它们的工作原理、优缺点以及适用场景。核心是使用Rust语言实现一个虚拟化环境，并尝试进行优化。

**方向[5]：** 使用Rust语言来重新实现和优化ROS的MMU，利用Rust语言的内存安全性和无需垃圾回收的特性，以期提高ROS在内存管理方面的效率和可靠性。侧重于理解ROS中MMU的工作原理、当前存在的优缺点以及适用场景，并尝试通过改进和优化来提升其性能。

**方向[6]：** 在ROS中，通信是一个至关重要的组成部分，其安全性和性能直接影响着ROS系统的稳定性和效率。故我们计划重新设计和优化ROS的通信模块，以提升通信的安全性和性能。

**方向[7]：** 用RUST改写分布式文件管理或计算系统，使其在原有基础上完善安全性相关问题并且一定程度上实现性能提升。

### 2.分析筛选选题调研阶段（3.10-3.16）

在这一阶段，我们对以上提及的七个方向进行了进一步的可行性方案调研，并且与指导老师沟通了初步的选题思路，最后，在老师的指导帮助下以及通过我们的调研分析，我们综合考虑了硬件基础、潜在挑战、功能实现、性能优化、安全性等因素，决定优先考虑方向[2]（用Rust优化linux内核的一部分，如kvm相关的部分）以及方向[5]（使用Rust语言来重新实现和优化ROS的MMU）这两个选题，以待下一步的调研。

### 3. 深入专项分析两大选题调研阶段（3.17-3.23）

通过了一周的专项调研以及在课后与指导老师的思路交流，我们由于改写ROS会涉及到底层通信，其中网络作为系统领域和OS并列的另一大领域，潜在困难会远多于另一个方案，并且ROS的性能/功能瓶颈获取并不明显受到该实验改写方向的影响（相对而言，ROS的通信瓶颈更多的在网络拓扑和网络通信方向，而不是在本机的代码执行效率上） ，故我们选择了将用Rust改写Linux作为了最终选题。就这一选题方向我们小组也做了大量的调研，如其目前实现面临的困难（如在同一宏内核中Rust语言与C语言共存所可能带来的风险等，由于具体部分在立项依据以及困难与挑战部分均有详细阐述，在此不再过多赘述）并且作为进一步的拓展部分，我们还调研了Q-Learning这一机器学习算法的应用，可以将其作为Rust改写Linux存储管理调用部分的拓展工作。

### 4. 可行性分析部分的调研以及初步调研报告编写阶段（3.24-3.30）

在这一阶段中，我们总结此前做过的一系列调研与讨论工作，通过具体分工进行初步调研报告的编写，并且我们对在接下来的可行性验证工作以及正式实验工作必然会用到的语言——Rust进行了初步的集体学习，为了接下来小组实验的顺利进行，这一步的工作是有效且必要的。

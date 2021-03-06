---
sort: 6
---
# 操作系统：设计与实现（南大）


- [Yanyan's Wiki](http://jyywiki.cn/)
- [操作系统：设计与实现 (2022 春季学期)](http://jyywiki.cn/OS/2022/)


WHY WHAT HOW

## 为什么学操作系统

```note
为什么学微积分？

做题和背后的东西。newton时代的启蒙和应用，后来cauchy时代的严格化与公理化。

现在教科书一上来就是极限的定义，以后后面的微积分的计算技巧。

这些技巧帮助我考上了研究生。

但是，还是要问，为什么学微积分呢？

现代应用：优化理论，有限元，现代工业的基础，控制理论。

经典的反例，线性代数。定义定理习题定理习题....
```

为什么学操作系统。学了C语言没地方用，很可惜。

这些每天用的东西，比如键盘鼠标显示屏，如果我能知道背后的原理，那一定是一件很有意思的事情。

浏览器，编译器，IDE，游戏外挂。

虽然但是，学完操作系统也搞不出来这些（LOL），但是，学完操作系统给了编程全部的知识，除了语法。 

如果不经过一些理论上的学习，直接啃linux内核源码是件很崩溃的事情。想知道一个函数是怎么用的，点开，跳转，一层层跳，跳了10层发现就是一个简单的表达`__x = __y`，这不直接崩溃？它太复杂了，内核源码是数万工程师几十年积累的结果。

所以不妨先用一个最小的，但是原理上完全一样的东西来看看是怎么工作的。再看linux源码就可以把东西映射到这个简单的最小系统上，然后就知道linux是怎么工作的了。这就可以真的去调试内核了。


```note
当然还有一点，会操作系统以后，工资水平，还行。

我相信我在物质财富积累足够以后，精神境界也会有追求的。啥解决卡脖子，做中国人的操作系统，都行。
```

## 什么是操作系统

操作系统是一个软件体，让应用程序更容易的运行起来（甚至允许您似乎同时运行多个程序），允许程序共享内存，允许程序与设备交互，以及其他类似的有趣的东西。

电脑上接个机械臂，能让他动起来。（这是我的专业，ROS机器人操作系统，很有意思）

<figure>
    <img src="./os.png" width=500>
</figure>


“精准”的教科书定义没啥用。

操作系统如何从一开始编程现在这样的？一个东西都是慢慢从简单变得复杂的。

三个重要线索：
- 计算机（硬件）
- 程序（软件）
- 操作系统（管理软件的软件）

狭义的操作系统：
- 对单一计算机硬件系统作出抽象、支撑程序执行的软件系统


1940年的计算机，很简单，很大，是个状态机。是个时序逻辑电路，甚至都不如一个本科课程上用FPGA实现的CPU。但是有很多天才的创造，真空管实现逻辑门，存储器用延迟线（delay lines，很有意思），输入输出是带着洞的纸带。

这是个正经的图灵机。

[ENIAC Simulator and Related Material](https://www.cs.drexel.edu/~bls96/eniac/)

这时候的计算机根本不需要操作系统，程序直接用指令操作硬件，不需要画蛇添足的用操作系统这个程序来管理。

1950年就广泛应用了，有了晶体管逻辑门，更大更快的磁芯内存，更多的IO设备。而且IO设备的速度已经严重低于处理器的速度，于是1953年出现了中断机制。

这时候要考虑这代码怎么写，不可能说IDE用C，那时候的程序员在卡片上打孔，这些程序也像是编译器输出的机器码。

这个时期，计算机非常昂贵，一个大学就只有一台，一群人排队等着用。这时候就逐渐有操作系统的概念开始出来了。好多个卡片但是只有一个读卡机，需要一个东西管理这些卡片，批处理。这时候操作系统还可以提供一些API，能不能把结果在写在卡片上，这就是“文件”（真物理文件）。

1960年，集成电路发展，有了巨大的内存，可以同时把好几个卡片放入内存了，不需要换卡了。但是还是只有一个CPU，这时候怎么办呢。看程序的话，有段时间在用CPU，有段时间在把结果写到文件，这看起来是很自然的，但是这时候操作IO打印机是比较慢的。这是很自然的想法，在等待打印的时候CPU是不是可以用起来呢？

这里有了多道程序的概念。多个程序载入内存，又有设计上的要求，内存最好隔离开，因为不同人写的程序会有BUG，一个坏的程序破坏好的程序是很难过的。这时候为了让多个程序互不干扰，硬件上也有了变化，很基本的想法，有了这个年代的操作系统，与现在的很接近了。

这就有了进程的概念。进程在执行 I/O 时，可以将 CPU 让给另一个进程，在多个地址空间隔离的程序之间切换。虚拟存储使一个程序出 bug 不会 crash 整个系统。操作系统中自然地增加进程管理 API。

既然操作系统已经可以在程序之间切换，为什么不让它们定时切换呢？现在操作系统是计算机的最高管理者，不管程序是谁，执行100ms必须停下来让另一个去走。这就有了基于中断的机制。Multics，1965年，有了。现代操作系统诞生。

1970年就有了个人电脑。这时候的计算机和现在的计算机并没有太多区别了。今年能用计算机干的事情1970年的计算机也能干。上天入地，那个时候就有人工智能了。甚至可以在字符终端上处理字符了，上古时期的word。

1972年C语言也出现了。

1973年，信号API（进程间通信）、管道、grep

1983：socket，GNU，

再后来，

1984 MaxOS

1985 Windows

1991 Linux 0.01

1993 Windows NT

1996 Debian

2002 Windows XP

2004 Ubuntu

2007 IOS

2008 Android

2015 Windows


```note
一本书：《unix传奇》，讲贝尔实验室。

一帮人聚在一起，有一些想法，用一些代码实现它。

今天也是，有个小团队，有个小想法，看看能不能实现。
```

今天的操作系统，通过 “虚拟化” 硬件资源为程序运行提供服务的软件。

空前复杂的系统之一
- 更复杂的处理器和内存
  - 非对称多处理器 (ARM big.LITTLE; Intel P/E-cores)
  - Non-uniform Memory Access (NUMA)
  - 更多的硬件机制 Intel-VT/AMD-V, TrustZone/SGX, TSX, ...
- 更多的设备和资源
  - 网卡、SSD、GPU、FPGA...
- 复杂的应用需求和应用环境
  - 服务器、个人电脑、智能手机、手表、手环、IoT/微控制器……

非对称处理器，Intel大小核。个人PC都可以装上64G或者128G的RAM。


这里不讲这些，仅仅是讲一个最简单的原理性的小系统。

理解操作系统的三个根本问题

操作系统服务谁？
- 程序 = 状态机
- 课程涉及：多线程 Linux 应用程序

(设计/应用视角) 操作系统为程序提供什么服务？
- 操作系统 = 对象 + API
- 课程涉及：POSIX + 部分 Linux 特性

(实现/硬件视角) 如何实现操作系统提供的服务？
- 操作系统 = C 程序
  - 完成初始化后就成为 interrupt/trap/fault handler
- 课程涉及：xv6, 自制迷你操作系统

## 如何学操作系统？

首先是合格的操作系统用户
- 会 STFW/RTFM 自己动手解决问题
- 不怕使用任何命令行工具
    - vim, tmux, grep, gcc, binutils, ...
- 不惧怕写代码
  - 能管理一定规模 (数千行) 的代码
  - 能在出 bug 时默念 “**机器永远是对的**、我肯定能调出来的”
    - 然后开始用正确的工具/方法调试

```note
自己解决问题，比如学linux，需要这个环境。能在网上找到这个教程，跟着做下来。这其实也不是很困难。
```

成为操作系统的Power User，

其次是学会写代码

写代码=创造有趣的东西
- 命令行+浏览器
- 不需要语言特性，特意去讲设计模式
  - 当代码大到一定程度，自然就会关注这些东西

---
title:  OS-virtualation
date: 2024-09-04 12:00:00 +/-TTTT
categories: [OS]
tags: [OS]  # TAG names should always be lowercase
---
# OS
**based on 《oprating system : three easy piece》**
**Hence the role of the OS as a resource manager.**
## Introduction
### Virtualizing the CPU
* time sharing
  
### Virtualizing Memory
* space sharing
  
>Indeed, that is exactly what is happening here as the OS is virtualizing memory. Each process accesses its own private virtual address space(sometimes just called its address space)

### Concurrency
### persistent
### Design Goal
>One of the most basic goals is to build up some abstractions in order to make the system convenient and easy to use. 

>One goal in designing and implementing an operating system is to
 provide high performance; another way to say this is our goal is to minimize the overheads of the OS

> Another goal will be to provide protection between applications, aswell as between the OS and applications.

## virtualation-cpu
### Abstraction Of Process
#### Intro
* def of process
>The definition of a process, informally, is quite simple: it is a running program [V+65,B70].

* 在进行CPU虚拟化后，不断的切换会导致运行速度下降
>. This basic technique, known as time sharing of the CPU, allows users to run as many concurrent processes as they would like; the potential cost is performance, as each will run more slowly if the CPU(s) must be shared.

* 采用两级的策略来使得cpu正常运行
>To implement virtualization of the CPU, and to implement it well, the
OS will need both some low-level machinery as well as some high-level
intelligence.

* 策略将考虑以下元素
>A scheduling policy in the OS will make this decision, likely using historical information (e.g., which program has run more over the last minute?),
workload knowledge (e.g., what types of programs are run), and performance metrics (e.g., is the system optimizing for interactive performance,or throughput?) to make its decision.

#### Process Creation 
* 启动步骤
> By loading the code and static data into memory, by creating and ini tializing a stack, and by doing other work as related to I/O setup, the OS
 has now (fifinally) set the stage for program execution. It thus has one last
 task: to start the program running at the entry point, namely main().

>By jumping to the main() routine (through a specialized mechanism that
we will discuss next chapter), the OS transfers control of the CPU to the
newly-created process, and thus the program begins its execution.

* 为何现代的技术使用慢加载而不是快加载
  * 快加载将内容完全载入后才运行
  * 慢加载加载部分就运行
>To truly understand how lazy loading
 of pieces of code and data works, you’ll have to understand more about the machinery of paging and swapping

#### Process States
 ![alt text](https://raw.githubusercontent.com/huazZengblog/huazZengblog.github.io/main/_posts/img/OSprocessState.png)

#### Data Struction
* process control block PCB
### Mechanism: Limited Direct Execution
#### Intro
* challenge in time sharing
  * 性能：
    > The first is performance: how can we implement virtualiztion without adding excessive overhead to the system?  

  * 控制
    > The second is control: how can we run processes efficiently while retaining control over the CPU ?
* two problem in direct execution
  *  The “direct execution” part of the idea is simple: just run the program directly on the CPU；把CPU的控制权交给process
  * 如何监管进程
  * 如何控制切换进程
  > The fifirst is simple: if we just run a
 program, how can the OS make sure the program doesn’t do anything that we don’t want it to do, while still running it effificiently? The second: then we are running a process, how does the operating system stop it from running and switch to another process, thus implementing the time sharing we require to virtualize the CPU?

#### Problem #1: Restricted Operations
* 引入kernel mode 和user mode
  * user code 受限状态
   > code that runs in user mode is restricted in what it can do.
  * kernel mode 
   > In this mode, code that runs can do what it likes
* 转化为kernel 的流程
  * 运行trap指令 进入kernel模式
    * 在trap时需要保存好目前程序的相关信息
  * 执行命令
    * trap table 在启动时存储应该跳到的指令地址
  * return-from-trap instruction 返回user模式
* 如果能够找到trap table的地址 并修改 那就可以控制机器

#### Problem #2: Switching Between Processes
* 当程序在运行时，意味着OS并不在CPU上运行，那拿回控制权就是问题的关键
  
##### A Cooperative Approach: Wait For System Calls
* 等待process 调用systemcall or an illegal operation；从而拿回控制权
* 太被动了，遇到无限循环且不进行系统调用的process怎么办?
  
##### A Non-Cooperative Approach: The OS Takes Control
* 引入 timer interrupt：定期发送中断信号
* 中断时 需要保存相关的信息 便于返回

##### Saving and Restoring Context
* all the OS has to do is save a few register values
for the currently-executing process (onto its kernel stack, for example) and restore a few for the soon-to-be-executing process (from its kernel stack).
* 在过程中可能发生的两种寄存器存储恢复情况
  * 进入中断：
    > The first is when the timer interrupt occurs; in this case, the user register state of the running process is implicitly saved by the hardware, using the kernel stack of that process. 

  * 切换上下文：
  > The second is when the OS decides to switch from A to B;
  
####  Worried About Concurrency? 
To whet your appetite 激发你的食欲
* 在处理中断时拒绝其他中断：One simple thing an OS might do is disable interrupts during interrupt processing;
  *  问题：过久的中断会导致其他中断信息的丢失
   > disabling interrupts for too long could lead to lost interrupts, which is (in technical terms) bad
  
* 使用锁来保护数据
 > Operating systems also have developed a number of sophisticated locking schemes to protect concurrent access to internal data structures.


### Scheduling: Introduction

####  Workload Assumptions
* 想象总是美好的，假设并不是完全现实的，但是能够帮助我们前进
> The workload assumptions we make here are clearly unrealistic, but that is alright (for now), because we will relax them as we go, and eventually develop what we will refer to as ... (dramatic pause) ...a fully-operational scheduling discipline
* 假设
1. Each job runs for the same amount of time.
2. All jobs arrive at the same time.
3. All jobs only use the CPU (i.e., they perform no I/O)
4. The run-time of each job is known.

####  Scheduling Metrics
* Metrics
  *  turnaround time : The turnaround time of a job is defined
  as the time at which the job completes minus the time at which the job arrived in the system.
  $T{turnaround} = T{completion}-T{arrival} $ 
  *  fairness 
  *  response time: jobs到达 到jobs被执行的时间
     $T{response} = T{firstrun} − T{arrival} $
*  No policy is perfect.life,too.
> for example, may optimize performance but at the cost of preventing a few jobsfrom running, thus decreasing fairness. 

####  First In, First Out(FIFO)
* the jobs' running time difference may cause  convoy effect
  > a number of relatively-short potential consumers of a resource get queued behind a heavyweight resource consumer.
* Funny 
  > For example, grocery stores commonly have a “ten-items-or-less” line to ensure that shoppers with only a few things to purchase don’t get stuck behind the family preparing for some upcoming nuclear winter

#### Shortest Job First (SJF)
* 可以解决由不同job运行时长导致的问题
* 但当job并不是同时到达时，仍然存在convoy effect
*  SJF是non-preemptive schedule ： 不主动去暂停运行中的job的调度算法
#### Shortest Time-to-Completion First (STCF)
* 很容易想到，那我当新任务进来时，计算完成时间是多少，通过完成时间的早晚来调度
* STCF 是 preemptive schedule
* 但是 对于response time ，会导致一些问题，特别是在交互式的系统中，用户会过久的等待

#### Round Robin（RR）
* def ： 将时间切为时间片，通过时间片来做切换，每个job只运行一个时间片
* 因此，时间片切的越短，响应时间就会对应的越快；但是越多切换就会导致效率的下降；需要tradeoff
* 如果response time是唯一要考虑的因素，RR无疑是很好的，但RR会导致turnaround time很长
  * 考虑当三个相同job同时到达时，每个运行时间为5s，完成时间分别为 13 14 15，无疑是很差的选择

* 时间不仅仅是在寄存器的保存读取操作上，还有对当前job的一些处理都会在切换中被清除，重启时又要重新搭建
> Note that the cost of context switching does not arise solely from the OS actions of saving and restoring a few registers.they build up a great deal of state in CPU caches, TLBs, branch predictors,and other on-chip hardware. Switching to another job causes this stateto be flushed and new state relevant to the currently-running job to bebrought in, which may exact a noticeable performance cost [MB91].
 * 同时RR是fair，但fair 势必导致 更长的turnaround time；
   * you can’t have your cake and eat it too 连吃带拿是不现实的
  > Indeed, this is an inherenttrade-off: if you are willing to be unfair, you can run shorter jobs to completion, but at the cost of response time; if you instead value fairness,response time is lowered, but at the cost of turnaround time. This type of trade-off is common in systems; 

#### Incorporating I/O
* 当job进行I/O操作时，由于I/O操作的延迟，会被阻塞等待操作返回，这时候就可以调度其他job来执行
* overlap：When possible, overlap operations to maximize the utilization of systems. 重叠运行

#### No More Oracle
* 前面所有的内容都是基于我们知道job时间的假设 做的研究
* 而实际上我们并没有那么多信息 ***No More Oracle***
*  仍然没有解决OS无法预测未来的问题 我们将使用***multi-level feedback queue*** 来解决这个问题
> We have still not solved the problem of the fundamental inability of the OS to see into the future

### Scheduling: The Multi-Level Feedback Queue



### 按比例份额调度
* 彩票系统

## virtulation-memory
### Abstract address space

### address translation

### segamentation

### free space manage
找空间的方式

* 伙伴系统
* 分离空间列表

### paging


### TLB
地址翻译的buffer
* thrashing
### smaller page size
* bigger page
* multi-level
* 段页式
* inverted page
### swapping 策略
* fifo
* lru
* 


## Concurrency
### concurrency

### lock

### thread

### deadlock
见 OS-deadlock

### condition variable

见 OS-condition-variable


### semaphore

**解决同步互斥问题**


## persistent

### io设备
* 使用中断来控制
* dma ： 将与磁盘的交互给到dma设备 cpu无需处理这个交互

### 磁盘

* io时间 = 寻道时间+旋转+传输
* 磁盘调度算法
  * fifo
  * sstf
  * scan
  * spft

### 文件结构

* inode计算

### 文件链接

* 软链接
* 硬链接

## final review
**信号量**

**缓存区读写io时间计算**

**hard link soft link**



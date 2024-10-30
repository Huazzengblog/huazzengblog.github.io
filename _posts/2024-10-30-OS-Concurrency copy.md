---
title:  OS-Concurrency
date: 2024-10-30 12:00:00 +/-TTTT
categories: [OS]
tags: [OS ]  # TAG names should always be lowercase
---
# Concurrency

## An Introduction

### Parallelism vs Concurrency
- Parallelism: simultaneous execution of multiple tasks 多核处理
- Concurrency: simultaneous execution of multiple tasks that share a single processor  单核处理 时分复用

### Thread
共同拥有
#### **Multiple threads within a single process share:**
- Process ID (PID) 
- Address space
- Code (instructions) 
- Most data (heap) 
- Open file descriptors 
- Current working directory 
- User and group id 

各自拥有的
#### **Each thread has its own**
- Thread ID (TID) 
- Set of registers, including Program counter(**TCBs**) and Stack pointer 
- Stack for local variables and return addresses (in same address space)
#### User-level threads: Many-to-one thread mapping

- **实现**：由用户级运行时库实现
- **创建、调度和同步**：在用户级创建、调度和同步线程
- **操作系统无感知**：操作系统不感知用户级线程
- **操作系统认知**：操作系统认为每个进程只包含一个控制线程

##### 优点
- **不需要操作系统支持**：可移植性强
- **可调整调度策略**：满足应用程序需求
- **较低的开销**：线程操作无需系统调用

##### 缺点
- **无法利用多处理器**：不能充分利用多核处理器的优势
- **一个线程阻塞会导致整个进程阻塞**：当一个线程阻塞时，整个进程都会被阻塞

#### Kernel-level threads: One-to-one thread mapping 

- **实现**：操作系统为每个用户级线程提供一个内核线程
- **独立调度**：每个内核线程独立调度
- **线程操作**：线程的创建、调度和同步由操作系统完成

##### 优点
- **可以并行运行**：每个内核级线程可以在多处理器上并行运行
- **阻塞不影响其他线程**：当一个线程阻塞时，进程中的其他线程仍可以被调度

##### 缺点
- **较高的开销**：线程操作的开销较高
- **操作系统需良好扩展**：操作系统必须能够随着线程数量的增加而良好扩展






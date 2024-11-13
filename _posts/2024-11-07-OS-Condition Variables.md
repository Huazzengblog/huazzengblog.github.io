---
title:  OS-Condition Variables
date: 2024-11-07 12:00:00 +/-TTTT
categories: [OS]
tags: [OS ]  # TAG names should always be lowercase
---
### Concurrency Objectives
  • 互斥 mutual exclusion
两种状态：locked unlocked
实现方式：对资源加锁
  • spin lock：自旋锁 不断轮询问
  • Sleep lock：睡眠锁 休息一会再来问

  • 同步 Ordering
  解决方法：
  • 条件变量 Condition Variable 只能解决同步
  • 信号量 semaphores 既可以解决同步也可以解决互斥

### Condition Variables
There are many cases where a thread wishes to 
Check whether a condition is true before continuing its execution. 通过条件判断使得线程按顺序判定
Eg.父线程等待子线程结束才允许
通过子线程的条件变量来是的线程按顺序执行

实现方式：
  • Spin approach ：轮询条件变量 为1时继续执行
  • Wait and signal:
  需要互斥，防止共享变量被其他线程操作；
  Wait：等待条件变量的线程
  Signal：通知，多个等待则根据实现算法来选择第一个通知的对象;变条件变量的线程执行
  
  3 rules:
   
  
  
  错误的操作：
  在父线程获得锁后 因为信号已经发完了 所以父进程不会再被唤醒
  
  
  
  

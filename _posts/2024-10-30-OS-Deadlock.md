---
title:  OS-Deathlock
date: 2024-10-30 12:00:00 +/-TTTT
categories: [OS]
tags: [OS ]  # TAG names should always be lowercase
---
# Deathlock
## Conditions for Deadlock 
以下四条都满足 就可以生成死锁
* Mutual exclusion: 互斥

* Hold and wait:  持有资源 等待下一资源

* No preemption: 无法抢夺资源

* Circular wait:  环形等待

### Resource-Allocation Graph
通过绘制持有 请求的图，通过查看是否存在环来判断死锁
![alt text](https://raw.githubusercontent.com/huazZengblog/huazZengblog.github.io/main/_posts/img/deadlock-graph-a.png)

![alt text](https://raw.githubusercontent.com/huazZengblog/huazZengblog.github.io/main/_posts/img/deadlock-graph-a.png)
* If graph contains no cycles  no deadlock
* If graph contains a cycle 
  * if only one instance per resource type, then deadlock
  * if several instances per resource type, possibility of deadlock
  
## Methods for Handling Deadlocks
* Ensure that the system will never enter a deadlock state
    * Deadlock Prevention:通过破坏至少一个死锁条件来避免死锁
    * Deadlock Avoidance：判断当前条件是否会引起死锁来避免死锁
* Allow the system to enter a deadlock state and then recover（坏了修复）
* Ignore the problem and restart （鸵鸟）

### Deadlock Prevention
**We disallow one of the 3 policy conditions or use a protocol that prevents circular wait** 三种策略
导致效率低下
* 互斥性的破坏
  * Can not be banned.
* hold and wait 的破坏
  * 要么全申请 要么不申请
  * 将没用的资源释放出来
  * 效率低下 
* 无抢夺性的破坏
  * 允许进程去抢夺资源
* cycle的破坏
  * define a linear ordering for resources，once a resource is obtained, only those resources higher in the list can be obtained
  * 通过优先队列，来破坏环

### Deadlock Avoidance
Key ： **Stay in Safe State**
If a system is in safe state --》 no deadlocks
If a system is in unsafe state --》 possibility of deadlock
Avoidance ：  ensure that a system will never enter an unsafe state.

Algorithm:
* Allocation Graph Scheme
* Banker's Algorithm
#### Allocation Graph Scheme
* The request can be granted only if converting the request edge to an assignment edge does not result in the formation of a cycle in the resource allocation graph
* 保证没环就好了 ，保证资源释放后没环也可以
  
####  Banker’s Algorithm
对特定request进行判断
1. Initialize the system state:  
   * Available[j] = total amount of resource type j available in the system
   * Max[i][j] = maximum demand of resource type j by process i
   * Allocation[i][j] = amount of resource type j allocated to process i
   * Need[i][j] = Max[i][j] - Allocation[i][j]
   * Request[i][j] = amount of resource type j requested by process i

2. Available[j] = Available[j] - Request[i][j]
3. Allocation[i][j] = Allocation[i][j] + Request[i][j]
4. 验证现在剩余的资源是否满足需求，满足就继续，不满足就拒绝;
5. 如果找到序列能完成所有任务 则认定无死锁
   
###  Deadlock Detection
#### Detection Algorithm
1. Initialize the system state:  
   * Available[j] = total amount of resource type j available in the system
   * Max[i][j] = maximum demand of resource type j by process i
   * Allocation[i][j] = amount of resource type j allocated to process i
   * Need[i][j] = Max[i][j] - Allocation[i][j]
   * Request[i][j] = amount of resource type j requested by process i

2. Available[j] = Available[j] - Request[i][j]
3. Allocation[i][j] = Allocation[i][j] + Request[i][j]
4. 验证现在剩余的资源是否满足需求，满足就继续，不满足就拒绝;
5. 如果找到序列能完成所有任务 则认定无死锁
6. **如果由剩余则此处发生死锁**

#### Recovery Algorithm
* Abort all deadlocked processes
* Abort one process at a time until the deadlock cycle is eliminated
  * Selecting a victim – minimize cost
* Rollback – return to some safe state, restart process for that state
* Starvation –  same process may always be picked as victim, include number of rollback in cost factor

1. **中止所有死锁进程**：可以选择终止所有被死锁的进程。

2. **逐个中止进程，直到消除死锁循环**：也可以选择逐个中止进程，直到找到一种解决死锁的方法。

3. **选择中止的顺序**：
   - **进程的优先级**：可以根据进程的优先级来决定哪个进程先被中止。
   - **进程已经计算的时间以及完成所需的时间**：考虑进程已经运行了多长时间以及还需要多长时间才能完成。
   - **进程使用的资源**：查看进程已经占用的资源。
   - **进程完成所需的资源**：了解进程完成时还需要哪些资源。
   - **需要终止的进程数量**：评估有多少个进程需要被终止。
   - **进程是交互式还是批处理**：确定进程是交互式（与用户交互）还是批处理（自动运行）。

4. **选择受害者**：选择终止哪个进程应尽量降低成本。

5. **回滚**：将进程返回到某个安全状态，并从该状态重新启动进程。

6. **饥饿**：如果总是选择同一个进程作为受害者，可能会导致该进程一直被中止，从而出现饥饿现象。因此，应该将回滚的次数纳入成本因素中进行考虑。 

这些策略和考虑因素旨在有效地解决死锁问题，同时尽量减少对系统的影响。
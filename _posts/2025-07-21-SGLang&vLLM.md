---
title: SGLang&vLLM
date: 2025-07-21 12:00:00 +/-TTTT
categories: [AI,system]
tags: [AI,LLM,system ]
---
# SGlang&vLLM

## vLLM
paper : https://arxiv.org/abs/2309.06180

### vLLM核心技术--PageAttention
#### 知识基础
* KVcache
* Virtual memory、pagging 
#### 解决的痛点问题
先前的llm server system存在的问题：
* kvcache的存储分配依据请求的max_length来确定，导致了内部空间的浪费
* 不同请求的max_length不同，会导致碎片空间的出现，导致外部空间的浪费
以上情况与operate system中的文件系统的发展相似，基于此作者提出了PageAttention
#### Method
![alt text](https://raw.githubusercontent.com/huazZengblog/huazZengblog.github.io/main/_posts/img/SGlang&vllm/vllm_system_overview.png)
##### PAGing
* 设置固定大小的block，token增加时，不断分配block以存储kvcache
    * 一个请求可能存储在多个block上，最大的内部空间浪费限制于block的大小
    * block并非物理空间的连续 而是逻辑空间连续

* block的address translation
    * 通过kv cache manager进行翻译，将逻辑地址 翻译为 物理地址从而计算，可见下图翻译过程
![alt text](https://raw.githubusercontent.com/huazZengblog/huazZengblog.github.io/main/_posts/img/SGlang&vllm/vllm-translation.png)



##### applicaiton to decoding
* parallel sampling ：对一个请求生成多个答案
    * 对每个block加入一个标记位记录引用当前block的引用数
    * 在生成不同答案时，prompt相同，所以prompt所在的块引用数为答案数
    * 而后，在生成过程中的分叉处，采用copy on write 方法， 复制第一个不同的block，原先的block引用数-1
    * 在不同block中继续生成
* beam search
    * 与parallel sampling 接近，但是在beamsearch时，不仅共享prompt，还共享部分输出候选块

* shared prefix
    * 对于一些具有共同系统提示，或者前缀prompt的请求，可以将prefix进行共享，大量降低kvcache缓存需求

* mixed decoding

##### Scheduling and Preemption
调度与资源争夺，在操作系统中是极为重要的一部分，如何决定资源的优先级，如何选择被抢夺资源
在pageattention中由于LLM 的输入提示的长度可能会有很大差异，并且生成的输出长度不是先验的，这取决于输入提示和模型。随着请求数量及其输出的增长，vLLM 可能会耗尽 GPU 的物理块来存储新生成的 KV 缓存。需要解决以下两个问题：
1. 它应该驱逐哪些区块？

   基于“全有或全无”的驱逐策略（all-or-nothing eviction）：  
   - 对于单个序列（如一个用户请求的生成过程），要么将其所有KV缓存块全部转移到CPU，要么不转移（因为序列的所有块会被同时访问）。  
   - 对于“序列组”（如 beam search 中的多个候选序列），由于可能存在内存共享，整个组的所有序列会被同时转移（gang-scheduled）。

2. 如果再次需要，如何恢复被驱逐的块？

**1. Swapping（交换恢复）**
- **核心逻辑**：将被驱逐到CPU内存的KV缓存块重新复制回GPU内存，恢复序列的处理状态。
- **过程**：
  1. 当被暂停的序列需要继续处理时，vLLM从CPU内存中找到该序列对应的KV缓存块。
  2. 将这些块从CPU复制回GPU的物理块中，复用原有的内存分配信息。
  3. 序列在GPU上继续生成后续token，无需重新计算历史KV缓存。
- **依赖条件**：GPU与CPU之间的内存带宽（传输速度直接影响恢复延迟）。


**2. Recomputation（重新计算恢复）**
- **核心逻辑**：不保留被驱逐的KV缓存块，而是在需要恢复时，通过重新处理序列的历史输入（原始prompt + 已生成的token），重新计算出所有KV缓存块。
- **过程**：
  1. 将原始prompt与已生成的token拼接成一个新的完整输入序列。
  2. 用模型对这个新序列进行一次前向传播（类似“提示阶段”的批量计算），一次性生成所有位置的KV缓存。
  3. 由于是批量计算（而非逐token生成），重新计算的延迟可能比原始生成过程更低。
- **依赖条件**：GPU的计算能力（算力越强，重新计算速度越快）。

在实际应用中，vLLM会根据硬件环境（如CPU-GPU带宽、GPU算力）选择更高效的方案。例如：
- 若CPU-GPU带宽高（如NVLink），Swapping可能更快。
- 若GPU算力强且序列较短，Recomputation可能更高效。

### 性能

## SGLang
paper : https://arxiv.org/abs/2312.07104
### 重要特性




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
* **Language primitives**
* **RadixAttention**
* **Compressed finite state machine**
* **API speculative execution**


### Language primitives

SGLang 的核心是一个**内嵌于 Python 的领域特定语言**，它简化了创建复杂 LLM 应用程序的过程。

* **高级原语 (Primitives)**: 该语言提供了简单而强大的命令（原语）来控制生成过程。关键原语包括：
    * `gen`: 用于从模型生成文本。
    * `select`: 用于从给定列表中选择最可能的选项。
    * `fork` 和 `join`: 用于并行执行多个生成调用并合并结果。
    * `image` 和 `video`: 用于原生处理多模态输入。
* **简化的工作流**: 此模型抽象了繁琐的任务，如手动进行字符串操作和管理并行 API 调用。这使得开发人员可以使用熟悉的 Python 语法轻松创建复杂的程序，例如智能体工作流和高级提示技术。



### RadixAttention

SGLang 运行时的一项主要优化是 **RadixAttention**，这是一种用于自动重用键值（KV）缓存的新颖技术。KV 缓存存储了 LLM 推理过程中的中间计算结果，重用它可以显著加快处理速度。

* **问题**: 许多 LLM 程序，如少样本学习或多轮聊天，会重复使用相同的初始文本（前缀），例如系统提示或对话历史。标准的推理系统通常在每次新调用时都为这些共享前缀重新计算 KV 缓存，浪费了时间和资源。
* **SGLang 的解决方案**: RadixAttention 将整个 KV 缓存视为一个由 **radix 树**（一种前缀树）管理的共享资源。
    * 这种数据结构可以有效地查找并重用跨不同请求的任何共享前缀的缓存数据。
    * 当需要内存来处理新请求时，它使用“最近最少使用”（LRU）策略来驱逐旧的缓存条目，从而确保高效的内存管理。
    * 一个**缓存感知调度器 (cache-aware scheduler)** 会优先处理那些能够实现最高缓存重用率的请求，从而进一步提高吞吐量。

这种对 KV 缓存的系统性重用极大地减少了冗余计算，实现了更大的批处理大小和更低的时延。

### Compressed finite state machine

对于需要 LLM 以特定结构化格式（例如 JSON）生成输出的任务，SGLang 使用**压缩有限状态机（FSMs）**来加速解码。

* **问题**: 强制 LLM 遵循特定语法（如 JSON 模式）通常是通过在生成的每一步检查并只允许有效词元 (token) 来完成的。这种逐词元验证的方式速度很慢，特别是对于结构的固定部分，如 `"summary": "`。
* **SGLang 的解决方案**: SGLang 会分析语法（以正则表达式形式提供）并创建一个压缩的 FSM。
    * 它会识别任何固定且没有替代路径的字符序列。
    * 然后它将这个序列**压缩成 FSM 中的单个转换**。
    * 这使得运行时可以在一个步骤内解码整个多词元序列，而不是一次只解码一个词元，从而极大地加快了结构化输出的生成速度。

### API Speculative Execution

SGLang 还为那些依赖外部黑盒 LLM API（例如来自 OpenAI 的 API）且无法修改其底层推理引擎的程序引入了一项优化。

* **问题**: 一个需要进行多次顺序 API 调用来构建单个结果的程序（例如，先生成一个名字，然后用该名字生成一个职位名称），每次调用都会产生时延和成本。
* **SGLang 的解决方案**: 它使用 **API 推测执行**。在序列的第一个 API 调用中，它指示模型生成比立即需要的词元多几个额外的词元。然后，SGLang 解释器会保留这些额外的词元，并尝试用它们来满足程序中下一步生成的需求。如果模型正确地预测了程序的流程，后续的 API 调用就可以完全避免，从而节省了时间和金钱。



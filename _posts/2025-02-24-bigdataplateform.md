---
title: big data platform
date: 2025-02-25 00:45:00 +/-TTTT
categories: 25spring
tags: [25spring]
---
# 大数据平台技术

## 存储
###  GFS

* HDFS
* 多master结构，如何处理
* mapreduce
  * map
  * shuffling
  * reduce
  * cons
    * 1.每个流程结束后，下个流程才能启动，如果节点性能不同，会导致延迟
      *   solution1：保持节点性能一致，逐步替代掉性能差的节点
      *   solution2: 平台技术，加持mapreduce
          *   haloop ：尽可能保持节点不阻塞
          *   spark ：构建平台时候分析map和reduce的依赖关系，自动调度，在特定情况下不等待上一流程结束
  * row vs column （storage）

|                            | Row Storage                                                        | Column Storage                                                                                                  |
| -------------------------- | ------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------- |
| **Definition**             | Data is stored row by row                                          | Data is stored column by column                                                                                 |
| **Storage Efficiency**     | Can be inefficient for large datasets, especially with sparse data | More efficient for large datasets, especially for selective queries                                             |
| **Read/Write Speed**       | Faster for retrieving full rows                                    | Faster for reading specific columns                                                                             |
| **Use Case**               | OLTP (Online Transaction Processing)                               | OLAP (Online Analytical Processing)                                                                             |
| **Data Compression**       | Less efficient compression due to mixed data types in rows         | Highly efficient compression due to homogeneous data types in columns (e.g., same type of data stored together) |

### RCFile
* RCFile（Row Column File）
    * 存储结构化数据的一种**列式**存储格式
    * 出现的背景
      * 快速数据加载
      * 快速查询处理
      * 高效的空间应用率
    * RCFile的数据布局和压缩

      * 数据布局：RCFile先将数据水平划分，再垂直划分，以行组（row group）为基本单位组织记录。
      * 数据压缩：头部元数据使用RLE算法压缩，表数据部分每列独立使用Gzip压缩算法。
    * RCFile的数据追加和读取

      * 数据追加：RCFile仅提供追加接口，数据追加时每个字段被拆开并追加到对应的列容器中。
      * 数据读取和惰性解压：RCFile处理行组时不需要将整个行组读入内存，只读取查询所需的头部元数据和特定列，支持惰性解压。

    * RCFile行组大小设置

      * 行组大小的考虑：较大的行组大小具有更好的数据压缩率，但会降低读取性能和惰性解压的优势；较小的行组大小具有更高的读取性能，但压缩率较低。

    * RCFile API简介

      * RCFile作为MapReduce输入输出：RCFile可以作为MapReduce程序的输入或输出，提供了InputFormat、RecordReader和OutputFormat的实现。
      * RCFileReader和RCFileWriter：提供了Reader和Writer接口，支持数据追加操作和多种读取方式。
  
### TSfile

* TSFile（Time Series File）
    * bloom filter
      * 通过多个hash 来判断一个物品是否 **一定不在或者可能在** 集合中，存在误判
    * 序列间索引
      ![alt text](https://raw.githubusercontent.com/huazZengblog/huazZengblog.github.io/main/_posts/img/bigdata/TSfile.png)
    * 序列内索引
      ![alt text](https://raw.githubusercontent.com/huazZengblog/huazZengblog.github.io/main/_posts/img/bigdata/TSfile-2.png)
    * 文件操作
      * 文件命名
      * 写入文件
        * 写入内存缓冲区
        * 持久化数据
        * 关闭文件

      * 读文件
        * 归并查询
          * 流程:
          为每个时间序列创建 FileSeriesReader。
          使用最小堆按时间戳对齐数据点。
          合并数据点生成 RowRecord。
          * 适用场景:
          无过滤条件或仅时间过滤。
        * 连接查询
          * 流程:
          初始化 TimeGeneratorImpl，生成满足条件的时间戳。
          为每个时间序列创建 FileSeriesReaderByTimestamp。
          根据时间戳查询数据点，生成 RowRecord。
          * 适用场景:
          包含值过滤条件。
        
        
      | 查询方式   | **归并查询（Merge Query）** | **连接查询（Join Query）** |
      |------------|------------------------|------------------------|
      | **数据获取方式** | **顺序扫描**多个时间序列数据 | **按筛选出的时间戳查询数据** |
      | **时间戳处理方式** | **最小堆合并（按时间对齐）** | **先筛选时间戳，再查询数据** |
      | **适用查询类型** | **无过滤条件 或 仅时间过滤** | **带值过滤条件** |
      | **计算复杂度** | 线性扫描 `O(N log M)` | 先筛选 `O(N) + 查询 O(log M)` |
      | **查询特点** | 高效批量处理大范围数据 | 精确查询满足条件的时间点数据 |
      | **索引依赖** | 不依赖索引，顺序合并 | 依赖时间索引，加速查询 |
      | **适用场景** | **时间序列分析、大规模查询** | **条件筛选、点查询** |


      
### IoTDB（Internet of Things Database）

| 特性                 | 说明                                                           |
| -------------------- | -------------------------------------------------------------- |
| **高效存储**         | 采用 **列式存储** + **时序压缩**，减少存储占用                 |
| **高吞吐写入**       | 适用于 **高频数据采集**，支持 **百万级点/秒** 写入             |
| **强大的查询能力**   | 支持 **SQL 语法** + **时序分析（合并、聚合、插值、降采样等）** |
| **数据生命周期管理** | 具备 **冷热分离存储、分区存储管理**                            |
| **高效索引机制**     | 采用 **时间序列索引（索引树 + 布隆过滤器）**，加速查询         |
| **轻量级**           | **可嵌入边缘设备**，适合 **边缘计算** 场景                     |
| **分布式架构**       | 具备 **分布式存储和计算能力**，支持 **高可用（HA）**           |


### something
* b树和b+树的区别 
| b树 | b+树 |
| --- | --- |
|b树是内存结构｜ b+树是外存结构｜
|b树是索引结构｜ b+树是数据结构｜

* b+树和lsm-tree的区别
｜ b+树 | lsm-tree |
| --- | --- |
| b+树有avl-tree的影子 | lsm-tree |

### bigtable

## 大数据计算引擎

* emacs

### mapreduce
#### 流程
* splitting 切分数据块 ， 基本为文件系统的块大小
* mapping 分别处理数据块
* shuffling 根据键值对整理mapping的输出，将相同key的数据分发到同一个reducer上
* reducing 统计最后结果
#### 瓶颈
* 计算量过大，mapping后的结果需要存储在本地，需要再shuffling和reducing阶段进行磁盘io
* mapping中的最慢节点
#### 代码设计原则
* 尽可能少传数据

### k-means
* limits
  * k是提前指定的


### data stream manegement system
* 流形数据的处理
* dsms vs dbms
  * dsms: 适合处理流式数据，不需要落盘
  * dbms: 适合处理静态数据，需要落盘
* 持续查询的样例
* some reference
  * reading in database system
### spark
* 基于hdfs 和 yarn ， 修改数据处理的模块 mapreduce
#### introduction
**why spark**:
* mapreduce 的局限性
  * 仅支持map reduce两种操作
  * 处理效率低下
    * map 结束才能reduce ， 若内存不足，则需要落盘，在后续阶段需要重新加载数据，导致效率低下
  
  * 不适应迭代计算，交互式处理，流式处理
* mr编程不够灵活


**in short** :
* 1. 基于内存计算，Spark将数据缓存于内存，大幅减少磁盘I/O，显著提升数据处理速度。
* 2. 运用DAG（有向无环图），Spark能对任务进行优化调度，合理安排任务执行顺序与依赖关系，提升资源利用率。  
* 3. 支持多线程处理，Spark可在单个节点内并行执行多个任务，提高计算效率，加速数据处理流程。 

#### detail
* wide dependency and narrow dependency
  * wide dependency: 宽依赖，即一个任务依赖多个任务，
  * narrow dependency: 窄依赖，即一个任务依赖一个任务，
##### pros in narrow dependency
* narrow dependency 节点可以流式传输数据，而不需要等待所有数据都处理完
  * 因为narrow dependency 节点的数据到下一节点后，直接做merge即可
> 说法1：narrow dependency 节点可以流式传输数据，而不需要等待所有数据都处理完
	•	Narrow dependency 指的是一个子分区只依赖于父RDD中的一个分区的数据。
	•	所以在这种情况下，每个分区之间没有 shuffle，数据可以逐个分区地计算和传输，而不需要等待所有分区计算完。
	•	因此，可以实现 pipelining（流水线）处理，边计算边传输，提高了效率。
##### pros in wide dependency
1. 智能调度 + DAG 优化
	•	Spark 会构建完整的 DAG（有向无环图），分析依赖关系后再执行。
	•	即使涉及 wide dependency，Spark 也能通过 DAG 提前知道哪些地方会 shuffle、哪些 task 可以合并、是否可以并行。
	•	对于一系列操作，Spark 会尽可能把 非 shuffle 的部分合并成前一 stage，最小化 shuffle 次数。

 MapReduce 是一次只执行一步，多个 Job 手动串联，不能整体优化。


2. 内存 + 磁盘混合存储
	•	shuffle 必然需要落磁盘（不能完全避免），但 Spark 的中间结果会尽量 缓存到内存中（memory + disk）。
	•	即使发生 shuffle，Spark 也不会像 MapReduce 那样每一步都强制写入 HDFS。
	•	如果内存够用，Spark 会把 RDD cache 起来，下游操作可以直接从内存读取。

 MapReduce 每次 reduce 输出都必须写入 HDFS，非常耗时。


3. 优化的 Shuffle 机制
	•	Spark 在 shuffle 时采用了多种优化策略，比如：
	•	合并小文件，避免产生海量中间文件
	•	数据压缩（Snappy、LZ4）
	•	Sort-based Shuffle，提高磁盘访问效率
	•	推测执行（Speculative Execution）：如果某个 task 运行太慢，Spark 会同时运行多个副本，谁快用谁，避免瓶颈节点。

  MapReduce shuffle 更原始，遇到慢节点容易造成瓶颈。


4. 资源管理和容错机制
	* 	Spark 可以动态分配资源，task fail 后会自动重试。
	* 	在 wide dependency 中，如果某个 reduce task 失败，只需 重新计算该分区，而不是整个 job。

    MapReduce 容错更粗暴，重试机制粒度更大。


#####  RDD 弹性分布式数据集



###### 一、什么是 RDD？

**RDD（Resilient Distributed Dataset）** 是 Spark 的核心数据结构，可以理解为：

> 🔹 一个 **分布式的、不可变的、支持并行计算的集合**。

它有几个核心特点：

| 特点                      | 解释                                                          |
| ------------------------- | ------------------------------------------------------------- |
| ✅ **分布式**              | 数据被划分成多个分区，分布在多个节点                          |
| ✅ **不可变**              | 一旦创建就不能修改，只能通过 Transformation 生成新 RDD        |
| ✅ **容错性（Resilient）** | 有 lineage 血统图，可以通过原始数据和操作链自动恢复丢失的数据 |
| ✅ **惰性求值**            | 只有触发 Action 才会真正执行计算                              |

💬 你可以把 RDD 看成是：**Spark 的基础数据容器 + 并行操作的接口**。


###### 🔧 二、Transformation（转换操作）

- **作用**：定义对 RDD 的转换逻辑，比如过滤、映射、聚合等。
- **特点**：**懒执行（lazy evaluation）**，不会立即运行，只是构建执行计划（DAG）。
- **返回值**：是一个新的 RDD。

### 常见的 Transformation 操作：

| 操作                | 含义                                                         |
| ------------------- | ------------------------------------------------------------ |
| `map(func)`         | 对每个元素应用函数，返回新的 RDD                             |
| `filter(func)`      | 筛选符合条件的元素                                           |
| `flatMap(func)`     | 类似 `map`，但可以返回多个元素                               |
| `groupByKey()`      | 将 (K, V) 按 K 分组                                          |
| `reduceByKey(func)` | 按 key 聚合，相比 groupByKey 更高效（会在 map 端做 combine） |
| `join(otherRDD)`    | 两个 RDD 按 key 进行连接                                     |

###### ⚡ 三、Action（行动操作）

- **作用**：触发真正的计算，将结果输出或返回给驱动端。
- **特点**：只有执行 Action，Spark 才会把上游的 Transformation 一起执行。
- **返回值**：返回普通值或写入外部系统（HDFS、数据库、控制台等）。

*  常见的 Action 操作：

| 操作                   | 含义                                     |
| ---------------------- | ---------------------------------------- |
| `collect()`            | 拉回所有数据到 driver（小数据量时用）    |
| `count()`              | 返回元素个数                             |
| `first()`              | 返回第一个元素                           |
| `take(n)`              | 返回前 n 个元素                          |
| `reduce(func)`         | 聚合所有元素                             |
| `saveAsTextFile(path)` | 结果写到 HDFS 或本地文件                 |
| `foreach(func)`        | 对每个元素执行函数（通常用于写外部系统） |


###### 🔁 四、它们之间的关系（超重要！）

1. 你对一个 RDD 反复做 Transformation，Spark 会**构建一个执行链（DAG）**，但**不会立刻执行**。
2. 直到你调用 Action（比如 `collect()` 或 `count()`），Spark 才会根据 DAG 触发计算。
3. 所有 Transformation 都是**惰性的**，这样 Spark 可以 **统一优化调度**，减少中间步骤和重复计算。

💡 所以 Transformation 是 “计划”，Action 是 “执行”。


###### 🎯 五、举个简单的例子：

```python
rdd = sc.textFile("data.txt")          # 读取文件，创建 RDD（初始 RDD）
rdd2 = rdd.map(lambda x: x.split())    # Transformation（延迟执行）
rdd3 = rdd2.filter(lambda x: len(x) > 3) # 继续 Transformation
result = rdd3.count()                  # Action：触发执行
```

- `textFile`, `map`, `filter` 都是 Transformation（只是构建图）
- `count()` 是 Action，会触发所有操作的执行流程

---

###### 🧠 一句话：

> **RDD 是 Spark 的数据基石；Transformation 是对数据的惰性转换；Action 是触发实际计算的“执行器”。理解它们的关系是玩转 Spark 的关键。**

---






```markdown
 Spark是一个快速、通用、可扩展的大数据处理引擎，以下从其基本概念、特点、架构、主要组件、应用场景等方面进行介绍：
- **基本概念**：Spark最初由加州大学伯克利分校的AMP实验室于2009年开发，是基于内存计算的大数据处理框架，旨在提供更快的数据分析和处理速度，以应对大规模数据的处理需求。它支持多种编程语言，如Scala、Java、Python和R等，为数据科学家和工程师提供了灵活的开发环境。
- **特点**
    - **快速**：Spark基于内存计算，将数据缓存在内存中，避免了频繁的磁盘I/O操作，大大提高了数据处理速度。与传统的基于磁盘的计算框架相比，Spark能够实现数十倍甚至上百倍的性能提升。
    - **易用**：提供了简洁的API，使得用户可以用熟悉的编程语言进行大数据处理。无论是进行简单的数据清洗、转换，还是复杂的机器学习算法实现，都可以通过Spark的API轻松完成。
    - **通用**：Spark提供了统一的平台，可用于批处理、交互式查询、实时流处理、机器学习、图计算等多种不同类型的大数据处理任务。
    - **可扩展性**：能够方便地扩展到大规模集群上，处理海量数据。它可以根据数据量和计算需求自动调整资源分配，实现高效的分布式计算。
- **架构**
    - **Driver Program**：负责创建SparkContext，协调和控制整个Spark应用程序的执行。它将用户编写的代码分发给各个Executor，并收集结果。
    - **Cluster Manager**：负责管理集群资源，如YARN、Mesos等。它接收Spark应用程序的资源请求，并分配资源给Executor。
    - **Executor**：是在集群节点上运行的进程，负责执行具体的任务。每个Executor都有自己的内存空间，用于存储和处理数据。
- **主要组件**
    - **Spark Core**：提供了Spark的基本功能，包括任务调度、内存管理、数据共享等。它是其他组件的基础，为上层应用提供了底层的支持。
    - **Spark SQL**：用于处理结构化数据的组件，提供了SQL查询接口和DataFrame、Dataset API。它可以将SQL查询与Spark的分布式计算能力相结合，方便用户对大规模结构化数据进行查询和分析。
    - **Spark Streaming**：支持实时流数据处理的组件，能够以高吞吐量和低延迟处理实时数据流。它将实时流数据分割成小的批次，然后使用Spark Core进行处理，实现实时的数据分析和处理。
    - **MLlib**：是Spark的机器学习库，提供了丰富的机器学习算法和工具，如分类、回归、聚类、协同过滤等。它基于DataFrame和Dataset API，使得机器学习任务的实现更加简单和高效。
    - **GraphX**：用于处理图数据的组件，提供了图计算的API和算法，如Pregel模型、PageRank算法等。它可以方便地对大规模图数据进行建模和分析。
- **应用场景**
    - **数据仓库**：可以作为数据仓库的计算引擎，与Hive等数据仓库工具结合，实现对大规模数据的快速查询和分析。
    - **实时流处理**：适用于处理实时产生的数据流，如网站日志、传感器数据等。可以实时对这些数据进行分析和处理，及时发现异常和趋势。
    - **机器学习**：为机器学习提供了强大的计算平台，能够快速处理大规模的训练数据，加速模型的训练和迭代。
    - **图计算**：可以用于处理各种图数据，如社交网络、知识图谱等。通过GraphX组件，可以方便地进行图的遍历、节点分类、链路预测等操作。
```


#### HDFS拓展
##### yarn 资源管理调度
Yarn是Hadoop生态系统中的一个重要组件，主要用于集群资源的管理和调度，其作用主要体现在以下几个方面：
- **资源管理**：Yarn可以对集群中的各种资源，如CPU、内存、磁盘、网络等进行统一的管理和监控。它能够收集各个节点的资源使用情况，包括已使用资源和可用资源，并将这些信息提供给调度器，以便进行合理的资源分配。
- **任务调度**：Yarn负责将用户提交的任务分配到集群中的各个节点上执行。它根据任务的资源需求和集群的资源状况，使用不同的调度算法，如先进先出（FIFO）、公平调度（Fair Scheduler）、容量调度（Capacity Scheduler）等，将任务合理地分配到各个节点上，以提高集群的资源利用率和任务执行效率。
- **多框架支持**：Yarn支持多种计算框架在同一个集群上运行，如MapReduce、Spark、Flink等。不同的计算框架可以共享集群的资源，Yarn会为每个框架分配相应的资源，使得它们能够在集群中并发执行，提高集群的整体利用率和灵活性。
- **提高集群利用率**：通过动态分配资源，Yarn可以根据任务的实际需求，将资源在不同的应用程序和任务之间进行灵活调配。当某个应用程序不需要使用大量资源时，Yarn可以将这些资源分配给其他有需求的应用程序，从而避免资源的闲置和浪费，提高整个集群的资源利用率。
- **提供可扩展性和高可用性**：Yarn具有良好的可扩展性，可以方便地添加新的节点到集群中，以满足不断增长的业务需求。同时，它还具备高可用性机制，通过主备节点等方式，确保在某个节点出现故障时，能够自动进行故障转移，保证集群的正常运行，减少对业务的影响。

好的，下面是对 **Hive 数据仓库** 和 **Zookeeper 集群管理** 的简要介绍：



##### Hive 数据仓库

- Hive 是构建在 Hadoop 之上的一个**数据仓库工具**，用于处理和分析大规模结构化数据。
- 它提供类 SQL 的查询语言（称为 HiveQL），**让用户可以使用类似 SQL 的方式操作 HDFS 上的数据**，无需直接编写 MapReduce 程序。
- 支持的数据操作包括：**数据提取（Extract）、转换（Transform）、加载（Load）**，统称 ETL。
- Hive 会将 HiveQL 查询语句**转换为底层的 MapReduce、Tez 或 Spark 作业**进行执行。
- 适用于离线批量处理场景，**不适合低延迟的实时查询**。

##### Zookeeper 集群管理

- Zookeeper 是一个开源的**分布式协调服务**，用于管理分布式系统中的元数据和协调任务。
- 在分布式环境中，多个服务之间需要共享状态、进行协作、保持一致性，Zookeeper 就提供了这样的基础设施。
- 主要功能包括：
  - **配置管理**：集中保存分布式系统的配置信息，支持动态更新。
  - **命名服务**：为分布式节点分配唯一名称，便于查找。
  - **分布式锁**：避免多个进程同时访问共享资源，确保并发安全。
  - **选举机制**：在主从架构中，用于选出主节点。
- 常见于 Hadoop、Kafka、HBase 等大数据组件中，用于协调和维护系统的稳定运行。

## paper reading
* pig latin is a language that is used to express data processing tasks in a way that is easy to understand and easy to translate into a sequence of map and reduce operations.
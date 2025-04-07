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


### k-means
* limits
  * k是提前指定的
  * 
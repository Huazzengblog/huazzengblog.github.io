---
title: llm-security
date: 2025-03-03 00:45:00 +/-TTTT
categories: 25spring
tags: [llm,security]
---
# llm-security
## paperreading
### data
####  Advances, challenges and opportunities in creating data for trustworthy AI
  * data design (the sourcing and documentation of data)
  * data sculpting (data selection, cleaning and annotation) 
  * data strategies for model testing and monitoring 

##### data design
* problem 
  * limited or biased coverage.
    * more paticipants
    * synthetic data
      * cons ： 1 与现实数据的差异 2 如果合成数据的源头也存在偏差，那么合成数据与现实数据之间的差异会更加明显
  
#### data sculpting
* data valuation
  * shapley score
  * ActiveClean
  * detect poor-quality data points.
  > By looking at data points whose human annotation systematically deviates from predictions made by AI models
* data annotation
  * cons  
    * expensive
    * sensitive data that could not be crowd-sourced(总包的)
  * reduce the manual annotation cost
    * data programming
    * active learning
    > reduce annotation costs is to prioritize the most valuable data for humans to annotate with active learning

#### Data to evaluate and monitor AI models
* 实时数据的处理问题
* MLOps

#### data strategies


---

# llm-supply chain
## define
### paper reading
#### An Empirical Study of Artifacts and Security Risks in the Pre-trained Model Supply Chain
* https://dl.acm.org/doi/10.1145/3560835.3564547
* 这篇论文主要对模型链中的安全问题进行研究，体取安全特征，比较了传统供应链和PTM（pre-trained model） 供应链的区别
* related work 中值得关注的点：
  * Adversarial Attacks On and With PTMs
    这里举出了PTM的攻击方法 ：
      * 数据投毒
      * 后门攻击
      * 副作用字节嵌入
        > Another malicious attack, the EvilModel, produces side-effects instead. Wang et al. show a PTM with malware bytes embedded into its neurons’ parameters, which are extracted and assembled into malware at runtime
* RQs
> RQ1 What is the typical structure of model hubs? 
> RQ2 What practices are in place to improve security among users of the model hubs? 
> RQ3 What are the potential threats in model hubs?

* method
  * dataset : model hub selection
  * RQ1
    * 分析modelhub的分类，任务，模型，数据集
    * 通过不同的属性，将他们分为三类
      * model contributing workflow
      * model distribution workflow
      * and model versioning
  * RQ2
    * 由于huggingface可以自由的 创建model hub，安全风险较高 所以基于huggingface 中的安全相关文档展开调查
  * RQ3
    * 对特定模型进行安全性检测，这里指的是使用模型yaml文件中声明的数据集对模型进行测试，对比实际结果与声明结果对区别


* conclusion
  * 
## attacker

## similarity

## mpc
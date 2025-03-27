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


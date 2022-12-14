# 李冲 - 高级研发工程师

教育经历 : 中国地质大学（北京）| 矿产普查与勘探 | 本硕 | 2020年毕业

[https://github.com/jony-lee](https://github.com/jony-lee)

[jonylee.cn@gmail.com](mailto:jonylee.cn@gmail.com)

17600023713

## 工作经历

### 滴滴 - 研发工程师（2020.7 - 至今）

负责开发和维护配置云同步服务（golang）、数据检查系统（golang）、特征数据挖掘系统（spark）、数据快修服务（golang）

### 滴滴 - 实习（2019.9 - 2020.6）

负责团队内LBS业务的评测质检SOP服务的后端开发。

服务面向公司内部SOP评测人员，采用`gin`作为服务的HTTP框架，mysql进行数据持久化。

期间，主要调研并完成了服务对`hive`数据的对接，对`OSS（对象存储）`数据的对接。在业务场景复杂度日益提高的背景下，完成了原生SQL至`gorm`的依赖迁移，同时抽象持久化层，完成了实例依赖变更为接口依赖的业务抽象，为后续的多个功能项开发提供了效率保障。

## 项目

### 配置云同步服务

**项目描述**： 服务面向C端用户提供个性化配置项云同步功能，服务以`gin`为web框架，使用`protobuf`协议传输数据，分布式`redis`服务提供高并发缓存及并发锁机制，`mysql`进行数据持久化。

**主要工作**： 负责业务方需求开发，项目go版本的升级（go1.9->go1.13以上）迁移。

### 数据快修服务

**项目描述**：为LBS服务提供数据实时快修功能，弥补常规数据发版流程慢，数据异常单点性的问题。面向前端和LBS服务，前端负责LBS快修数据录入，LBS服务负责实时获取快修数据。

使用`thrift`rpc通信框架，采用分布式增量数据同步方式，使用`mysql`行锁作为数据的分布式锁，保障数据同步的最终一致性。

**主要工作**：基于项目构建了一套对快修服务的AB实验验证系统，以客观评估快修效果，完成从前端实验添加，数据透传，指标计算和实验报告产出的评估系统。同时赋能LBS服务，提供关键的业务性指标。

### 特征数据挖掘系统

**项目描述**：基于LBS数据进行时空数据挖掘，并提供数据挖掘风险点位，透传C端完成用户体验的提升。

**主要工作**：作为项目owner，完成从0->1的开发和上线及多轮的数据挖掘优化迭代，业务指标上有显著提升。同时在项目中完成了一次TB级数据从3小时->0.5小时的批处理任务性能调优，在大数据开发中有一定的调优经验，项目主要的技术栈包含`sparkSQL` `scala` `hive`。

### 数据检查系统

**项目描述**：为LBS服务提供业务向的异常数据检查，通过线上流量离线回归服务，发现服务中显著不合理的业务数据问题。并发访问LBS服务并处理服务响应的数据，对数据结果进行去重、排序、输出问题数据及相关报告。技术栈包含`thrift` `protobuf` `channel` `并发锁`等。

**主要工作**：作为项目owner负责项目的搭建和投入应用，并使用`pprof`对golang项目进行过性能调优及内存异常泄漏的问题定位。

## 技能

- Golang `Gin` `Gorm` `pprof` `Channel`

- Spark `Scala` `SparkSQL` `PySpark` `Hadoop` `Hdfs`

- 其他 `javascript`（可视化工具开发`vue` `element-ui` `echarts`）
  
  - `python`（处理网络、数据、可视化等`pandas` `numpy` `matplotlib` `pyspark`）
  
  - `linux` `git` `shell` `applescript` 

## 自我评价

性格沉稳、喜好钻研技术问题，在校及工作期间面对问题，不自我设限，喜欢提出想法，喜欢尝试探索，同时对技术抱有热情，期待一起愉快的coding，用技术改变生活~

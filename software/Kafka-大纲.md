# Kafka 知多少 v1.0 — 目录大纲

> 基于 Apache Kafka 3.9.x | 重点关注 KRaft 模式

---

## 📖 前言
- Kafka 的行业地位与学习价值
- 文档适用读者与阅读建议
- 版本说明（基于 3.9.x，KRaft 为默认模式）

## 第一章：基础理论与架构演进
- 1.1 技术诞生背景与核心问题（LinkedIn 数据管道挑战）
- 1.2 架构演进历程（0.x ZooKeeper → 2.x Exactly-Once → 3.x KRaft 去 ZK）
- 1.3 核心概念术语表（中英文对照+通俗解释）
- 1.4 适用场景 vs 不适用场景（✅/❌）
- 1.5 同类技术对比（RocketMQ / Pulsar / RabbitMQ）

## 第二章：实现原理解析
- 2.1 核心组件架构图（Broker、Controller、Producer、Consumer）
- 2.2 消息生产流程（正常发送 + 重试 + 幂等）
- 2.3 消息消费流程（Consumer Group Rebalance + Offset 管理）
- 2.4 副本同步与 ISR 机制（正常同步 + Leader 故障切换）
- 2.5 关键算法/协议（KRaft Raft 协议、分区分配策略、Log Compaction）
- 2.6 数据模型与存储结构（Segment、Index、TimeIndex、磁盘布局）
- 2.7 通信协议（Kafka 自定义二进制协议）

## 第三章：源码级实现细节
- 3.1 代码组织结构（core/、clients/、raft/、metadata/）
- 3.2 关键数据结构（ProducerBatch、Log/LogSegment）
- 3.3 并发模型（Reactor 网络模型、Purgatory 延迟操作）
- 3.4 重要类/接口设计（KafkaApis、ReplicaManager、GroupCoordinator）
- 3.5 启动流程（KafkaServer.startup → 服务就绪）

## 第四章：设计模式深度剖析
- 4.1 架构层面模式（Controller 模式、KRaft 状态机）
- 4.2 GoF 设计模式实例（观察者、策略、构建者、模板方法）
- 4.3 分布式系统模式（Leader Election、ISR、Epoch Fencing）
- 4.4 设计权衡分析（吞吐 vs 延迟、一致性 vs 可用性）

## 第五章：高可靠与高可用方案
- 5.1 集群部署架构对比（单机/多 Broker/跨机房）
- 5.2 数据一致性保证（acks、ISR、Exactly-Once 语义）
- 5.3 故障检测与恢复（Controller Failover、Unclean Leader Election）
- 5.4 容灾策略（MirrorMaker 2、跨地域复制）
- 5.5 性能优化（30+ 调优参数）
- 5.6 可观测性（JMX Metrics、关键监控指标）

## 第六章：实战指南与问题排查
- 6.1 最佳实践（Topic 设计、分区数规划）
- 6.2 性能压测（kafka-producer-perf-test / kafka-consumer-perf-test）
- 6.3 常见故障模式（10+ 场景，含排查命令）
- 6.4 故障演练（Chaos Engineering）

## 第七章：面试题库与学习路径
- 7.1 高频面试题（基础→进阶→原理→场景，附答案）
- 7.2 学习资源推荐（官方文档、书籍、KIP）
- 7.3 实验环境搭建（Docker Compose KRaft 模式）

## 附录
- A. 术语表
- B. 参考文献
- C. 版本更新记录

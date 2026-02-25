# Redis 知多少 v1.0 — 完整目录大纲

> 基于 Redis 7.2.x / 7.4.x | 最后更新时间：2025-01-01

---

## 📖 前言

### 技术背景与学习价值
- Redis 的行业地位与应用广度
- 学习 Redis 的核心价值（工程实用性、理论深度、职业必备、生态完善）

### 适用读者
| 读者类型 | 建议阅读章节 |
|---------|------------|
| 初学者 | 前言 → 第一章 → 第七章（实验环境） |
| 后端开发工程师 | 第一章 → 第二章 → 第五章 → 第六章 |
| 运维/DBA 工程师 | 第五章 → 第六章 → 第二章 |
| 架构师 | 第二章 → 第四章 → 第五章 |
| 面试备战 | 第七章 → 第二章 → 第五章 |

### 版本说明
- 基于 Redis 7.2.x（最新稳定开源版本）
- 涵盖 7.4.x 许可证变更（BSD → RSALv2 + SSPLv1）等重要信息
- Redis 7.0 → 7.2 的重大变化概述

---

## 第一章：基础理论与架构演进

### 1.1 技术诞生背景与核心问题
- 时代背景：2009 年，Salvatore Sanfilippo（Antirez）的实时日志分析需求
- Redis 要解决的核心问题：高性能读写、丰富数据结构、原子操作、持久化
- 名称由来：Remote Dictionary Server

### 1.2 架构演进历程
- 版本演进表格（1.0 → 7.2.x）
  - 1.0（2009）：基础 KV 存储
  - 2.0（2010）：虚拟内存、Pub/Sub
  - 2.6（2012）：Lua 脚本、毫秒级过期
  - 2.8（2013）：部分重同步（PSYNC）
  - 3.0（2015）：Redis Cluster
  - 3.2（2016）：GEO 命令、SDS 优化
  - 4.0（2017）：Module 系统、PSYNC2、混合持久化
  - 5.0（2018）：Stream 数据类型
  - 6.0（2020）：ACL、I/O 多线程、RESP3 协议
  - 6.2（2021）：GETDEL/GETEX、COPY 命令
  - 7.0（2022）：Redis Functions、Sharded Pub/Sub、Multi-part AOF
  - 7.2（2023）：客户端缓存改进、性能优化
  - 7.4（2024）：许可证变更
- 演进原因分析

### 1.3 核心概念术语表
- 中英文对照表格（≥15 个术语）
  - SDS / Simple Dynamic String
  - Dict / 哈希表
  - Skiplist / 跳跃表
  - Ziplist / 压缩列表
  - Listpack / 紧凑列表
  - Quicklist / 快速列表
  - RDB / Redis Database
  - AOF / Append Only File
  - Sentinel / 哨兵
  - Cluster / 集群
  - Slot / 哈希槽
  - RESP / Redis Serialization Protocol
  - Pub/Sub / 发布订阅
  - Pipeline / 管道
  - Lua Scripting / Lua 脚本
  - Stream / 流
  - Module / 模块

### 1.4 适用场景 vs 不适用场景
- ✅ 适用场景：缓存、会话管理、排行榜、消息队列、实时计数器、分布式锁、地理位置、限流
- ❌ 不适用场景：海量冷数据存储、复杂关系查询、强事务 ACID、大 Value 存储

### 1.5 同类技术对比
- 对比表格：Redis vs Memcached vs etcd
  - 设计哲学
  - 数据结构支持
  - 持久化能力
  - 集群方案
  - 性能指标
  - 内存效率
  - 复杂度
  - 社区活跃度
  - 典型使用场景

---

## 第二章：实现原理解析

### 2.1 核心组件架构图
- Mermaid 架构图：客户端 → 网络层 → 命令解析 → 数据库引擎 → 持久化 → 复制

### 2.2 核心工作流程

#### 2.2.1 命令执行流程（正常流程）
- Mermaid 时序图：Client → Socket → EventLoop → CommandLookup → Execute → Reply

#### 2.2.2 主从复制流程
- Mermaid 时序图：全量同步（FULLRESYNC）与增量同步（PSYNC）

#### 2.2.3 Cluster 故障转移流程（异常流程）
- Mermaid 流程图：故障检测 → PFAIL → FAIL → 从节点选举 → 故障转移

#### 2.2.4 持久化流程
- RDB fork + COW 流程
- AOF 重写流程

### 2.3 关键算法/协议详解
- Gossip 协议（Cluster 节点通信）
- CRC16 哈希槽分配算法
- LRU / LFU 淘汰算法（近似 LRU 实现）
- Raft-like 选举算法（Sentinel 领导者选举）

### 2.4 数据模型与存储结构
- redisObject 结构（type + encoding + ptr）
- 五大基础类型 + 扩展类型的底层编码：
  - String → int / embstr / raw（SDS）
  - List → listpack / quicklist
  - Hash → listpack / hashtable
  - Set → intset / listpack / hashtable
  - Sorted Set → listpack / skiplist + hashtable
  - Stream → Rax tree + listpack
- 内存布局图（Mermaid）

### 2.5 通信协议
- RESP2 协议格式详解
- RESP3 协议新特性（6.0+）
- Inline 命令格式

---

## 第三章：源码级实现细节

### 3.1 代码组织结构
- src/ 核心模块功能说明表格
  - server.c / server.h — 服务器核心
  - networking.c — 网络通信
  - ae.c / ae.h — 事件驱动库
  - dict.c / dict.h — 哈希表实现
  - sds.c / sds.h — 动态字符串
  - t_string.c / t_list.c / t_hash.c / t_set.c / t_zset.c — 数据类型命令
  - rdb.c — RDB 持久化
  - aof.c — AOF 持久化
  - cluster.c — Cluster 实现
  - sentinel.c — Sentinel 实现
  - module.c — Module API

### 3.2 关键数据结构
- SDS（Simple Dynamic String）— C 代码展示
- dict（哈希表）— 渐进式 rehash 实现
- skiplist（跳跃表）— 节点结构与插入逻辑
- quicklist（快速列表）— ziplist/listpack 链表
- rax（基数树）— Stream 底层结构

### 3.3 并发模型
- 单线程事件循环模型（ae 库）
- I/O 多线程（6.0+）：读写分离的线程模型
- 后台线程（bio.c）：惰性删除、AOF fsync、文件关闭
- fork 子进程：RDB 持久化、AOF 重写

### 3.4 重要结构体/接口设计
- redisServer 结构体（全局状态）
- client 结构体（客户端连接）
- redisDb 结构体（数据库实例）
- redisObject 结构体（对象系统）
- redisCommand 结构体（命令表）
- 设计模式标注

### 3.5 启动流程
- main() → initServerConfig() → loadServerConfig() → initServer() → loadDataFromDisk() → aeMain()
- 流程图（Mermaid）

---

## 第四章：设计模式深度剖析

### 4.1 架构层面的模式
- Reactor 模式（ae 事件驱动库）
- Pipeline 模式（批量命令处理）
- Proxy 模式（Redis Cluster 的 MOVED/ASK 重定向）

### 4.2 GoF 设计模式
- 命令模式（Command Pattern）— redisCommand 命令表
- 观察者模式（Observer Pattern）— Pub/Sub 实现
- 策略模式（Strategy Pattern）— 内存淘汰策略
- 工厂模式（Factory Pattern）— redisObject 创建
- 模板方法模式（Template Method）— RDB/AOF 持久化框架

### 4.3 分布式系统模式
- Leader Election（Sentinel 领导者选举）
- Gossip Protocol（Cluster 节点发现与故障检测）
- Sharding（哈希槽分片）
- Write-Ahead Logging（AOF 持久化）

### 4.4 设计权衡分析
- 单线程 vs 多线程的权衡
- 内存 vs 磁盘的权衡
- 一致性 vs 可用性的权衡（AP 系统）
- 简单性 vs 功能完备性的权衡

---

## 第五章：高可靠与高可用方案

### 5.1 集群部署架构
- 单机模式
- 主从复制模式
- Sentinel 哨兵模式
- Cluster 集群模式
- 对比表格（适用场景、数据容量、可用性、复杂度）

### 5.2 数据一致性保证
- 异步复制机制
- WAIT 命令（同步等待复制）
- 最终一致性模型
- 脑裂问题与 min-replicas-to-write 配置

### 5.3 故障检测与恢复
- Sentinel 故障检测算法（SDOWN → ODOWN）
- Cluster 故障检测（PFAIL → FAIL）
- 自动故障转移流程
- 超时时间设置建议

### 5.4 容灾策略
- 多 AZ 部署方案
- 跨地域复制（Active-Active 概念）
- 备份与恢复策略（RDB 快照 + AOF）

### 5.5 性能优化（30+ 参数）
- 内存优化参数
- 网络优化参数
- 持久化优化参数
- 客户端优化参数
- 操作系统级优化
- 参数表格（参数名、默认值、建议值、说明）

### 5.6 可观测性
- INFO 命令各 section 详解
- SLOWLOG 慢查询日志
- Latency Monitor（延迟监控）
- CLIENT LIST 连接监控
- MEMORY USAGE / MEMORY DOCTOR
- 关键 Metrics 表格
- 健康检查接口
- 日志规范

---

## 第六章：实战指南与问题排查

### 6.1 最佳实践
- Key 命名规范
- Value 设计原则（避免 Big Key）
- 连接池配置
- 超时设置
- 序列化选择
- 缓存策略（Cache-Aside / Read-Through / Write-Behind）

### 6.2 性能压测
- redis-benchmark 使用指南
- memtier_benchmark 使用指南
- 典型指标解读（QPS、延迟、吞吐量）

### 6.3 常见故障模式（≥10 个）
1. 内存溢出（OOM）
2. Big Key 导致阻塞
3. Hot Key 导致单节点过载
4. 主从复制中断
5. Cluster 节点失联
6. 持久化导致延迟抖动（fork 耗时）
7. 客户端连接数耗尽
8. 慢查询导致阻塞（KEYS *、SMEMBERS 大集合）
9. 网络分区导致脑裂
10. AOF 文件损坏
11. 内存碎片率过高
12. Pub/Sub 消息积压

每个故障包含：现象描述 → 排查命令 → 根因分析 → 解决方案 → 预防措施

### 6.4 故障演练
- Chaos Engineering 场景设计
  - 主节点宕机模拟
  - 网络分区模拟
  - 内存压力测试
  - 持久化故障模拟

---

## 第七章：面试题库与学习路径

### 7.1 高频面试题

#### 基础题（5 题）
- Redis 支持哪些数据类型？
- Redis 为什么快？
- Redis 和 Memcached 的区别？
- Redis 的过期策略是什么？
- Redis 的持久化方式有哪些？

#### 进阶题（5 题）
- Redis 的内存淘汰策略有哪些？
- Redis 如何实现分布式锁？
- Redis 事务与 Lua 脚本的区别？
- Redis Pipeline 的原理和使用场景？
- Redis 的主从复制原理？

#### 原理题（5 题）
- Redis 单线程为什么能支撑高并发？
- Redis 的渐进式 rehash 是如何实现的？
- Redis Cluster 的哈希槽机制是什么？
- Redis 的 RESP 协议是如何设计的？
- Redis 的 RDB 和 AOF 混合持久化原理？

#### 场景题（5 题）
- 如何设计一个支持百万用户的排行榜？
- 如何解决缓存穿透、缓存击穿、缓存雪崩？
- 如何用 Redis 实现延迟队列？
- 如何处理 Redis Big Key 问题？
- Redis Cluster 扩缩容如何保证数据不丢失？

### 7.2 学习资源
- 官方文档必读章节
- 经典书籍推荐
- 论文推荐
- 优质博客/视频

### 7.3 实验环境搭建
- 单机 Docker 部署
- Docker Compose 主从 + Sentinel 部署
- Docker Compose Cluster 部署

---

## 附录

### A. 术语表
### B. 参考文献
### C. 版本更新记录（7.0 → 7.2 → 7.4 关键变更）

---

> 本文档最后更新时间：2025-01-01

---
title: ZooKeeper 基础概念
summary: ZooKeeper 基础概念
date: 2023-12-01
tags:
  - 中间件
  - ZooKeeper
  - 分布式
---
## ZK 是什么？
面向分布式应用的分布式协调服务

## 在分布式架构中起什么作用？

1. **配置管理**：ZooKeeper作为配置中心，存储和管理分布式应用的配置信息，实现配置的动态更新和一致性。

 2. **领导者选举**：通过ZooKeeper的领导者选举机制，分布式系统可以选出一台领导者，确保系统在任何时候都有一致的指导者，适用于分布式协调和管理。

 3. **分布式锁**：ZooKeeper提供了分布式锁的实现，确保在分布式环境中对共享资源的访问是有序的，避免竞态条件。

 4. **命名服务**：ZooKeeper可以作为分布式系统的命名服务，提供唯一的命名空间，帮助应用程序发现和定位服务。

 5. **实时通知与协调**：ZooKeeper的监听机制能够实现实时通知，用于协调分布式系统中各个节点的状态变化，确保系统的实时性和一致性。

 6. **分布式队列**：通过ZooKeeper的有序节点特性，可以实现分布式环境下的队列服务，用于任务调度和消息处理。

 7. **数据发布与订阅**：ZooKeeper的监听器机制可以用于实现数据的发布与订阅模式，帮助节点感知数据的变化并及时作出相应。

 8. **分布式协同**：ZooKeeper提供了轻量级的分布式协同能力，使得多个节点能够协同工作，共同完成任务，适用于分布式计算场景。

## 主要特点

- **高性能**数据保存在内存中，实现高吞吐量和低延迟。
- **高可用**只要大多数服务器可用，ZooKeeper 服务就可用。
  [1]![image](https://zookeeper.apache.org/doc/r3.8.3/images/zkservice.jpg)

## 基础概念
### 理解 ZooKeeper 的数据模型和分层命名空间
[2]![image](https://zookeeper.apache.org/doc/r3.8.3/images/zknamespace.jpg)
#### 数据模型

ZooKeeper 的数据模型类似于文件系统，使用一种层次化的结构，其中节点称为 ZNode（ZooKeeper Node）。每个 ZNode 都可以存储少量的数据，并且可以组织成一个树状结构，形成分层次的数据存储。

#### 分层命名空间

ZooKeeper 的分层命名空间是指 ZNode 可以通过路径进行组织，路径由斜杠（/）分隔，类似文件系统的路径。每个 ZNode 的路径都唯一标识了它在整个 ZooKeeper 命名空间中的位置。

#### 举例说明

考虑一个 ZooKeeper 命名空间的例子：

- /app
  - /config
    - server1
    - server2
  - /logs
    - log1
    - log2

在这个例子中，`/app` 是根节点，下面有两个子节点 `/config` 和 `/logs`。每个子节点下又可以有更多的子节点。例如，`/config` 下有两个子节点 `server1` `和server2`。每个 ZNode 都可以存储相关的数据，形成了一个树状结构。

#### 总体理解

- **数据模型：** ZNode 是 ZooKeeper 中的基本数据单元，类似于文件系统的文件，每个 ZNode都有一个路径唯一标识。
- **分层命名空间：** 数据通过路径的方式组织成分层次的命名空间，路径表示 ZNode 在整个层次结构中的位置。

ZooKeeper 通过路径的唯一性保证了数据的唯一性和易于管理。
## 文献引用
 [1,2]: [ZooKeeper Overview](https://zookeeper.apache.org/doc/r3.8.3/zookeeperOver.html)
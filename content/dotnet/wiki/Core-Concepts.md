---
title: "核心概念"
linkTitle: "核心概念"
weight: 20
date: 2024-01-19
description: >
  Durable Task Framework的 核心概念
---

> https://github.com/Azure/durabletask/wiki/Core-Concepts

### Task Hub

Task Hub 是命名空间内服务总线实体的逻辑容器。Task Hub worker 使用这些实体在代码协调和它们协调的活动之间可靠地传递消息。

### 任务活动

任务活动是执行特定协调步骤的代码片段。任务活动可以从任务协调代码中 "调度"。这种调度产生了一个普通的 .NET 任务，该任务可以（异步）等待，并与其他类似任务组成，以构建复杂的协调。

### 任务编排

任务协调安排任务活动，并围绕代表活动的任务构建代码协调。

### Task Hub Worker

Worker 是任务协调和活动的主机。它还包含对 Task Hub 本身执行 CRUD 操作的 API。

### Task Hub Client

Task Hub Client提供

- 用于创建和管理任务协调实例的 API
- 用于从 Azure 表查询任务协调实例状态的 API

Task Hub Worker 和 Task Hub client 都配置了服务总线的连接字符串，还可选择配置存储帐户的连接字符串。服务总线用于存储任务协调实例和任务活动之间执行和消息传递的控制流状态。但是，服务总线并不是一个数据库，因此当代码协调完成后，状态就会从服务总线中移除。如果配置了  Azure Table storage，那么只要用户将状态保存在该账户中，就可以对其进行查询。

![](https://github.com/Azure/durabletask/wiki/images/concepts.png)

该框架提供了任务协调（TaskOrchestration）和任务活动（TaskActivity）基类，用户可以从这些基类派生指定自己的协调和活动。然后，他们可以使用 TaskHub API 将这些协调和活动加载到进程中，然后启动 Worker 开始处理创建新协调实例的请求。

TaskHubClient API 可用于创建新的协调实例、查询现有实例并在需要时终止这些实例。
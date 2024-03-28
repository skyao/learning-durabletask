---
title: "编排实例管理"
linkTitle: "编排实例管理"
weight: 60
date: 2024-01-19
description: >
  在 Durable Task Framework 中编排实例管理
---

> https://github.com/Azure/durabletask/wiki/Orchestration-Instance-Management

TaskHubClient API 允许用户创建新的协调实例、查询已创建协调实例的状态并终止这些实例。

创建协调实例的 API 将返回实例信息。该信息可在后续 API 中用于查询实例的状态。

```c#
OrchestrationInstance instance = client.CreateOrchestrationInstance(typeof (EncodeVideoOrchestration), "http://<azurebloblocation>/MyVideo.mpg");

OrchestrationState state = client.GetOrchestrationState(instance);
Console.WriteLine(state.Name + " " + state.OrchestrationStatus + " " + state.Output);
```

返回的实例还可用于终止协调：

```c#
OrchestrationInstance instance = client.CreateOrchestrationInstance(typeof (EncodeVideoOrchestration),"http://<azurebloblocation>/MyVideo.mpg");
// 不好的事情发生了
client.TerminateInstance(instance);
```

请注意，实例查询方法要求使用 Azure 存储连接字符串创建 task hub。如果未提供连接字符串，则所有实例查询方法都将抛出 InvalidOperationException 异常。




---
title: "IOrchestrationService"
linkTitle: "IOrchestrationService"
weight: 10
date: 2024-01-19
description: >
 IOrchestrationService 的流程源码分析
---



Azure-functions-durable-extension 仓库下的    `src\WebJobs.Extensions.DurableTask\DurabilityProvider.cs`



The backend storage provider that provides the actual durability of Durable Functions. This is functionally a superset of IOrchestrationService and IOrchestrationServiceClient. If the storage provider does not any of the Durable Functions specific operations, they can use this class directly with the expectation that only those interfaces will be implemented. All of the Durable Functions specific methods/operations are virtual and can be overwritten by creating a subclass.

提供持久函数实际持久性的后端存储提供商。在功能上，它是 IOrchestrationService 和 IOrchestrationServiceClient 的超集。如果存储提供商不进行任何特定的 Durable Functions 操作，他们可以直接使用该类，并期望只实现这些接口。所有 Durable Functions 特定的方法/操作都是虚拟的，可以通过创建子类来覆盖。



## 类定义



```c#
    public class DurabilityProvider :
        IOrchestrationService,
        IOrchestrationServiceClient,
        IOrchestrationServiceQueryClient,
        IOrchestrationServicePurgeClient,
        IEntityOrchestrationService
        {}
```


---
title: "AzureStorageDurabilityProvider"
linkTitle: "AzureStorageDurabilityProvider"
weight: 100
date: 2024-01-19
description: >
 AzureStorageDurabilityProvider 的流程源码分析
---



Azure-functions-durable-extension 仓库下的    `src\WebJobs.Extensions.DurableTask\AzureStorageDurabilityProvider.cs`



*The Azure Storage implementation of additional methods not required by IOrchestrationService.*

IOrchestrationService 不需要的其他方法的 Azure 存储实现。



## 类定义



```c#
    internal class AzureStorageDurabilityProvider : DurabilityProvider {......}
```





DurabilityProvider 在 `src\WebJobs.Extensions.DurableTask\DurabilityProvider.cs`



## LockNextOrchestrationWorkItemAsync() 方法

这个方法的实现在基类 DurabilityProvider 中，AzureStorageDurabilityProvider 没有覆盖这个基类方法。


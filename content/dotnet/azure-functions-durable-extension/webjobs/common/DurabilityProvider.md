---
title: "DurabilityProvider"
linkTitle: "DurabilityProvider"
weight: 100
date: 2024-01-19
description: >
 DurabilityProvider 的流程源码分析
---



Azure-functions-durable-extension 仓库下的    `src\WebJobs.Extensions.DurableTask\DurabilityProvider.cs`



The backend storage provider that provides the actual durability of Durable Functions. This is functionally a superset of IOrchestrationService and IOrchestrationServiceClient. If the storage provider does not any of the Durable Functions specific operations, they can use this class directly with the expectation that only those interfaces will be implemented. All of the Durable Functions specific methods/operations are virtual and can be overwritten by creating a subclass.

提供持久函数实际持久性的后端存储提供商。在功能上，它是 IOrchestrationService 和 IOrchestrationServiceClient 的超集。如果存储提供商不进行任何特定的 Durable Functions 操作，他们可以直接使用该类，并期望只实现这些接口。所有 Durable Functions 特定的方法/操作都是虚拟的，可以通过创建子类来覆盖。



## 类定义

这个类实现了一堆接口：

```c#
    public class DurabilityProvider :
        IOrchestrationService,
        IOrchestrationServiceClient,
        IOrchestrationServiceQueryClient,
        IOrchestrationServicePurgeClient,
        IEntityOrchestrationService
        {}
```



## 构造函数



```c#
        /// <summary>
        /// Creates the default <see cref="DurabilityProvider"/>.
        /// </summary>
        /// <param name="storageProviderName">The name of the storage backend providing the durability.</param>
        /// <param name="service">The internal <see cref="IOrchestrationService"/> that provides functionality
        /// for this classes implementions of <see cref="IOrchestrationService"/>.</param>
        /// <param name="serviceClient">The internal <see cref="IOrchestrationServiceClient"/> that provides functionality
        /// for this classes implementions of <see cref="IOrchestrationServiceClient"/>.</param>
        /// <param name="connectionName">The name of the app setting that stores connection details for the storage provider.</param>
        public DurabilityProvider(string storageProviderName, IOrchestrationService service, IOrchestrationServiceClient serviceClient, string connectionName)
        {
            this.name = storageProviderName ?? throw new ArgumentNullException(nameof(storageProviderName));
            this.innerService = service ?? throw new ArgumentNullException(nameof(service));
            this.innerServiceClient = serviceClient ?? throw new ArgumentNullException(nameof(serviceClient));
            this.entityOrchestrationService = service as IEntityOrchestrationService;
            this.connectionName = connectionName ?? throw new ArgumentNullException(connectionName);
        }
```

这里的 

```c#
            this.entityOrchestrationService = service as IEntityOrchestrationService;
```

而这里的 servcie 是 IOrchestrationService 类型：

`IOrchestrationService service`



## LockNextOrchestrationWorkItemAsync()

这个方法的实现委托给 this.entityOrchestrationService 了：

```c#
        /// <inheritdoc/>
        Task<TaskOrchestrationWorkItem> IEntityOrchestrationService.LockNextOrchestrationWorkItemAsync(TimeSpan receiveTimeout, CancellationToken cancellationToken)
            => this.entityOrchestrationService.LockNextOrchestrationWorkItemAsync(receiveTimeout, cancellationToken);
```


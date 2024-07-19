---
title: "IOrchestrationService"
linkTitle: "IOrchestrationService"
weight: 10
date: 2024-01-19
description: >
 IOrchestrationService 的源码分析
---

Orchestration Service interface for performing task hub management operations and handling orchestrations and work items' state

协调服务接口，用于执行任务中心管理操作，处理协调和工作项状态

代码实现自在 Durabletask 仓库下的    `src\DurableTask.Core\IOrchestrationService.cs`

## 接口定义

IOrchestrationService 是一个接口：

```c#
    public interface IOrchestrationService
    {}
```



## 方法定义



### Start

```c#
        /// <summary>
        /// Starts the service initializing the required resources
        /// </summary>
        Task StartAsync();
```

### Stop

```c#
        /// <summary>
        /// Stops the orchestration service gracefully
        /// </summary>
        Task StopAsync();

        /// <summary>
        /// Stops the orchestration service with optional forced flag
        /// </summary>
        Task StopAsync(bool isForced);
```



### Create

```c#
        /// <summary>
        /// Deletes and Creates the necessary resources for the orchestration service and the instance store
        /// </summary>
        Task CreateAsync();

        /// <summary>
        /// Deletes and Creates the necessary resources for the orchestration service and optionally the instance store
        /// </summary>
        Task CreateAsync(bool recreateInstanceStore);

        /// <summary>
        /// Creates the necessary resources for the orchestration service and the instance store
        /// </summary>
        Task CreateIfNotExistsAsync();
```

### Delete

```c#
        /// <summary>
        /// Deletes the resources for the orchestration service and the instance store
        /// </summary>
        Task DeleteAsync();

        /// <summary>
        /// Deletes the resources for the orchestration service and optionally the instance store
        /// </summary>
        Task DeleteAsync(bool deleteInstanceStore);
```



### LockNextTaskOrchestrationWorkItem

等待下一个协调工作项并返回协调工作项，这个方法是重点。

```c#
        /// <summary>
        ///     Wait for the next orchestration work item and return the orchestration work item
        /// </summary>
        Task<TaskOrchestrationWorkItem> LockNextTaskOrchestrationWorkItemAsync(TimeSpan receiveTimeout, CancellationToken cancellationToken);

```



### LockNextTaskActivity



```c#
        /// <summary>
        ///    Wait for an lock the next task activity to be processed 
        /// </summary>
        Task<TaskActivityWorkItem> LockNextTaskActivityWorkItem(TimeSpan receiveTimeout, CancellationToken cancellationToken);

```


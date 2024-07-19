---
title: "IOrchestrationServiceClient"
linkTitle: "IOrchestrationServiceClient"
weight: 20
date: 2024-01-19
description: >
 IOrchestrationServiceClient 的源码分析
---

Interface to allow creation of new task orchestrations and query their status.

允许创建新任务协调和查询其状态的界面。

代码实现自在 Durabletask 仓库下的    `src\DurableTask.Core\IOrchestrationServiceClient.cs`

## 接口定义

IOrchestrationServiceClient 是一个接口：

```c#
    public interface IOrchestrationServiceClient
    {}
```



## 方法定义



### CreateTaskOrchestration

```c#
        /// <summary>
        /// Creates a new orchestration
        /// </summary>
        /// <param name="creationMessage">Orchestration creation message</param>
        /// <exception cref="OrchestrationAlreadyExistsException">Will throw an OrchestrationAlreadyExistsException exception If any orchestration with the same instance Id exists in the instance store.</exception>
        /// <returns></returns>
        Task CreateTaskOrchestrationAsync(TaskMessage creationMessage);

        /// <summary>
        /// Creates a new orchestration and specifies a subset of states which should be de duplicated on in the client side
        /// </summary>
        /// <param name="creationMessage">Orchestration creation message</param>
        /// <param name="dedupeStatuses">States of previous orchestration executions to be considered while de-duping new orchestrations on the client</param>
        /// <exception cref="OrchestrationAlreadyExistsException">Will throw an OrchestrationAlreadyExistsException exception If any orchestration with the same instance Id exists in the instance store and it has a status specified in dedupeStatuses.</exception>
        /// <returns></returns>
        Task CreateTaskOrchestrationAsync(TaskMessage creationMessage, OrchestrationStatus[] dedupeStatuses);

```

创建新的协调，并指定应在客户端去重复的状态子集

### SendTaskOrchestrationMessage

为协调发送新信息

> TBD: 这个方法不太理解在做什么？

```c#
        /// <summary>
        /// Send a new message for an orchestration
        /// </summary>
        /// <param name="message">Message to send</param>
        /// <returns></returns>
        Task SendTaskOrchestrationMessageAsync(TaskMessage message);

        /// <summary>
        /// Send a new set of messages for an orchestration
        /// </summary>
        /// <param name="messages">Messages to send</param>
        /// <returns></returns>
        Task SendTaskOrchestrationMessageBatchAsync(params TaskMessage[] messages);

```



### WaitForOrchestration

```c#
        /// <summary>
        /// Wait for an orchestration to reach any terminal state within the given timeout
        /// </summary>
        /// <param name="instanceId">Instance id of the orchestration</param>
        /// <param name="executionId">Execution id of the orchestration</param>
        /// <param name="timeout">Maximum amount of time to wait</param>
        /// <param name="cancellationToken">Task cancellation token</param>
        Task<OrchestrationState> WaitForOrchestrationAsync(
            string instanceId, 
            string executionId,
            TimeSpan timeout, 
            CancellationToken cancellationToken);
```



### ForceTerminateTaskOrchestration

```c#
        /// <summary>
        /// Forcefully terminate the specified orchestration instance
        /// </summary>
        /// <param name="instanceId">Instance to terminate</param>
        /// <param name="reason">Reason for termination</param>
        Task ForceTerminateTaskOrchestrationAsync(string instanceId, string reason);
```



### GetOrchestrationState



```c#
        /// <summary>
        /// Get a list of orchestration states from the instance storage for the most current execution (generation) of the specified instance.
        /// </summary>
        /// <param name="instanceId">Instance id</param>
        /// <param name="allExecutions">True if method should fetch all executions of the instance, false if the method should only fetch the most recent execution</param>
        /// <returns>List of OrchestrationState objects that represents the list of orchestrations in the instance store</returns>
        Task<IList<OrchestrationState>> GetOrchestrationStateAsync(string instanceId, bool allExecutions);

        /// <summary>
        /// Get a list of orchestration states from the instance storage for the specified execution (generation) of the specified instance.
        /// </summary>
        /// <param name="instanceId">Instance id</param>
        /// <param name="executionId">Execution id</param>
        /// <returns>The OrchestrationState of the specified instanceId or null if not found</returns>
        Task<OrchestrationState> GetOrchestrationStateAsync(string instanceId, string executionId);


```



### GetOrchestrationHistory



```c#
        /// <summary>
        /// Get a string dump of the execution history of the specified orchestration instance specified execution (generation) of the specified instance
        /// </summary>
        /// <param name="instanceId">Instance id</param>
        /// <param name="executionId">Execution id</param>
        /// <returns>String with formatted JSON representing the execution history</returns>
        Task<string> GetOrchestrationHistoryAsync(string instanceId, string executionId);

```



### PurgeOrchestrationHistory

清除超过指定阈值时间的协调实例状态和历史记录。还会清除 Blob 存储。

```c#
        /// <summary>
        /// Purges orchestration instance state and history for orchestrations older than the specified threshold time.
        /// Also purges the blob storage.
        /// </summary>
        /// <param name="thresholdDateTimeUtc">Threshold date time in UTC</param>
        /// <param name="timeRangeFilterType">What to compare the threshold date time against</param>
        Task PurgeOrchestrationHistoryAsync(DateTime thresholdDateTimeUtc, OrchestrationStateTimeRangeFilterType timeRangeFilterType);
```


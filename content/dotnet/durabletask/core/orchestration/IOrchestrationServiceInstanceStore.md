---
title: "IOrchestrationServiceInstanceStore"
linkTitle: "IOrchestrationServiceInstanceStore"
weight: 30
date: 2024-01-19
description: >
 IOrchestrationServiceInstanceStore 的源码分析
---

Instance Store provider interface to allow storage and lookup for orchestration state and event history

实例存储提供商接口，允许存储和查找协调状态和事件历史记录

代码实现自在 Durabletask 仓库下的    `src\DurableTask.Core\IOrchestrationServiceInstanceStore.cs`

## 接口定义

IOrchestrationServiceInstanceStore 是一个接口：

```c#
    public interface IOrchestrationServiceInstanceStore
    {}
```



## 方法定义



### 生命周期相关的方法

```c#
        /// <summary>
        /// Gets the maximum length a history entry can be so it can be truncated if necessary
        /// </summary>
        /// <returns>The maximum length</returns>
        int MaxHistoryEntryLength { get; }

        /// <summary>
        /// Runs initialization to prepare the instance store for use
        /// </summary>
        /// <param name="recreate">Flag to indicate whether the store should be recreated.</param>
        Task InitializeStoreAsync(bool recreate);

        /// <summary>
        /// Deletes instances instance store
        /// </summary>
        Task DeleteStoreAsync();
```

### Entity 相关的方法



```c#
        /// <summary>
        /// Writes a list of history events to instance store
        /// </summary>
        /// <param name="entities">List of history events to write</param>
        Task<object> WriteEntitiesAsync(IEnumerable<InstanceEntityBase> entities);

        /// <summary>
        /// Get a list of state events from instance store
        /// </summary>
        /// <param name="instanceId">The instance id to return state for</param>
        /// <param name="executionId">The execution id to return state for</param>
        /// <returns>The matching orchestration state or null if not found</returns>
        Task<IEnumerable<OrchestrationStateInstanceEntity>> GetEntitiesAsync(string instanceId, string executionId);

        /// <summary>
        /// Deletes a list of history events from instance store
        /// </summary>
        /// <param name="entities">List of history events to delete</param>
        Task<object> DeleteEntitiesAsync(IEnumerable<InstanceEntityBase> entities);

```



### GetOrchestrationState

获取指定实例的协调状态列表

```c#
        /// <summary>
        /// Gets a list of orchestration states for a given instance
        /// </summary>
        /// <param name="instanceId">The instance id to return state for</param>
        /// <param name="allInstances">Flag indication whether to get all history execution ids or just the most recent</param>
        /// <returns>List of matching orchestration states</returns>
        Task<IEnumerable<OrchestrationStateInstanceEntity>> GetOrchestrationStateAsync(string instanceId, bool allInstances);

        /// <summary>
        /// Gets the orchestration state for a given instance and execution id
        /// </summary>
        /// <param name="instanceId">The instance id to return state for</param>
        /// <param name="executionId">The execution id to return state for</param>
        /// <returns>The matching orchestration state or null if not found</returns>
        Task<OrchestrationStateInstanceEntity> GetOrchestrationStateAsync(string instanceId, string executionId);
```



OrchestrationStateInstanceEntity 是这样的：

```c#
    public class OrchestrationStateInstanceEntity : InstanceEntityBase
    {
        /// <summary>
        /// The orchestration state for this instance entity
        /// </summary>
        public OrchestrationState State;
    }
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



### OrchestrationHistory 相关的方法



```c#

        /// <summary>
        /// Gets the list of history events for a given instance and execution id
        /// </summary>
        /// <param name="instanceId">The instance id to return history for</param>
        /// <param name="executionId">The execution id to return history for</param>
        /// <returns>List of history events</returns>
        Task<IEnumerable<OrchestrationWorkItemInstanceEntity>> GetOrchestrationHistoryEventsAsync(string instanceId, string executionId);

        /// <summary>
        /// Purges history from storage for given time range
        /// </summary>
        /// <param name="thresholdDateTimeUtc">The datetime in UTC to use as the threshold for purging history</param>
        /// <param name="timeRangeFilterType">What to compare the threshold date time against</param>
        /// <returns>The number of history events purged.</returns>
        Task<int> PurgeOrchestrationHistoryEventsAsync(DateTime thresholdDateTimeUtc, OrchestrationStateTimeRangeFilterType timeRangeFilterType);

```





### JumpStartEntities 相关的方法



```c#
        /// <summary>
        /// Writes a list of jump start events to instance store
        /// </summary>
        /// <param name="entities">List of jump start events to write</param>
        Task<object> WriteJumpStartEntitiesAsync(IEnumerable<OrchestrationJumpStartInstanceEntity> entities);

        /// <summary>
        /// Deletes a list of jump start events from instance store
        /// </summary>
        /// <param name="entities">List of jump start events to delete</param>
        Task<object> DeleteJumpStartEntitiesAsync(IEnumerable<OrchestrationJumpStartInstanceEntity> entities);

        /// <summary>
        /// Get a list of jump start events from instance store
        /// </summary>
        /// <returns>List of jump start events</returns>
        Task<IEnumerable<OrchestrationJumpStartInstanceEntity>> GetJumpStartEntitiesAsync(int top);
```


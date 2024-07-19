---
title: "Fetch work item 流程"
linkTitle: "Fetch work item"
weight: 20
date: 2024-01-19
description: >
  Fetch work item 流程
---



## 入口

`src\DurableTask.Core\TaskOrchestrationDispatcher.cs`

```c#
        protected Task<TaskOrchestrationWorkItem> OnFetchWorkItemAsync(TimeSpan receiveTimeout, CancellationToken cancellationToken)
        {
            if (this.entityBackendProperties?.UseSeparateQueueForEntityWorkItems == true)
            {
               // duruale function quickstart 走到这里
                // only orchestrations should be served by this dispatcher, so we call
                // the method which returns work items for orchestrations only.
                Console.WriteLine("OnFetchWorkItemAsync: UseSeparateQueueForEntityWorkItems == true");
                Console.WriteLine("OnFetchWorkItemAsync: this.entityOrchestrationService=" + this.entityOrchestrationService?.GetType().FullName);
                return this.entityOrchestrationService!.LockNextOrchestrationWorkItemAsync(receiveTimeout, cancellationToken);
            }
            else
            {
                 // sample 走到这里
                // both entities and orchestrations are served by this dispatcher,
                // so we call the method that may return work items for either.
                return this.orchestrationService.LockNextTaskOrchestrationWorkItemAsync(receiveTimeout, cancellationToken);
            }
        }
```



### AzureStorageOrchestrationService

`src\DurableTask.AzureStorage\AzureStorageOrchestrationService.cs`

```c#
        public Task<TaskOrchestrationWorkItem> LockNextTaskOrchestrationWorkItemAsync(
            TimeSpan receiveTimeout,
            CancellationToken cancellationToken)
        {
            ......
            return LockNextTaskOrchestrationWorkItemAsync(entitiesOnly: false, cancellationToken);
        }
```



```c#
        async Task<TaskOrchestrationWorkItem> LockNextTaskOrchestrationWorkItemAsync(bool entitiesOnly, CancellationToken cancellationToken)
        {
          
          // This call will block until the next session is ready
          session = await this.orchestrationSessionManager.GetNextSessionAsync(entitiesOnly, linkedCts.Token);
```

### OrchestrationSessionManager

`src\DurableTask.AzureStorage\OrchestrationSessionManager.cs`



```c#
        public async Task<OrchestrationSession?> GetNextSessionAsync(bool entitiesOnly, CancellationToken cancellationToken)
        {
          // bool entitiesOnly,所以这里的 readyForProcessingQueue 是 orchestrationsReadyForProcessingQueue
                      var readyForProcessingQueue = entitiesOnly? this.entitiesReadyForProcessingQueue : this.orchestrationsReadyForProcessingQueue;
          
                      while (!cancellationToken.IsCancellationRequested)
            {
                // This call will block until:
                //  1) a batch of messages has been received for a particular instance and
                //  2) the history for that instance has been fetched
                        // 关键在这里
                LinkedListNode<PendingMessageBatch> node = await readyForProcessingQueue.DequeueAsync(cancellationToken);

                        
        }
```



这里的 readyForProcessingQueue （也即是 orchestrationsReadyForProcessingQueue），看看它是怎么 enqueue 的。



### orchestrationsReadyForProcessingQueue

orchestrationsReadyForProcessingQueue 定义在这里：

```c#
readonly AsyncQueue<LinkedListNode<PendingMessageBatch>> orchestrationsReadyForProcessingQueue = new AsyncQueue<LinkedListNode<PendingMessageBatch>>();
```



```c#
async Task ScheduleOrchestrationStatePrefetch(
            LinkedListNode<PendingMessageBatch> node,
            Guid traceActivityId,
            CancellationToken cancellationToken)
        {
            PendingMessageBatch batch = node.Value;

            AnalyticsEventSource.SetLogicalTraceActivityId(traceActivityId);
            
            try
            {
                if (batch.OrchestrationState == null)
                {
                    OrchestrationHistory history = await this.trackingStore.GetHistoryEventsAsync(
                       batch.OrchestrationInstanceId,
                       batch.OrchestrationExecutionId,
                       cancellationToken);

                    batch.OrchestrationState = new OrchestrationRuntimeState(history.Events);
                    batch.ETag = history.ETag;
                    batch.LastCheckpointTime = history.LastCheckpointTime;
                    batch.TrackingStoreContext = history.TrackingStoreContext;
                }

                if (this.settings.UseSeparateQueueForEntityWorkItems
                    && DurableTask.Core.Common.Entities.IsEntityInstance(batch.OrchestrationInstanceId))
                {
                    this.entitiesReadyForProcessingQueue.Enqueue(node);
                }
                else
                {
                    // 在这里 enqueue
                    this.orchestrationsReadyForProcessingQueue.Enqueue(node);
                }
            }
  ......
```



这个 ScheduleOrchestrationStatePrefetch 有两处引用：

1. 自己调用自己: 如果发生异常，则需要重新调用自身

   ```c#
   async Task ScheduleOrchestrationStatePrefetch(
               LinkedListNode<PendingMessageBatch> node,
               Guid traceActivityId,
               CancellationToken cancellationToken)
           {
               try
               {            
                 catch (Exception e)
               {
   
                   // Sleep briefly to avoid a tight failure loop.
                   // 短暂睡眠，避免出现紧密的故障循环。
                   await Task.Delay(TimeSpan.FromSeconds(5));
   
                   // This is a background operation so failure is not an option. All exceptions must be handled.
                   // To avoid starvation, we need to re-enqueue this async operation instead of retrying in a loop.
                   // 这是一个后台操作，因此不能失败。必须处理所有异常。
                   // 为避免出现饥饿状态，我们需要重新queue 这个异步操作，而不是循环重试。
                   await Task.Run(() => this.ScheduleOrchestrationStatePrefetch(node, traceActivityId, cancellationToken));
               }
   ```

   

2. 在AddMessageToPendingOrchestration() 方法中调用

   ```c#
           internal void AddMessageToPendingOrchestration(
               ControlQueue controlQueue,
               IEnumerable<MessageData> queueMessages,
               Guid traceActivityId,
               CancellationToken cancellationToken)
           {
             foreach (MessageData data in queueMessages)
             {
               ......
                        // If there is no batch for this instanceID-executionID pair, create one
                       if (targetBatch == null)
                       {
                           targetBatch = new PendingMessageBatch(controlQueue, instanceId, executionId);
                           node = this.pendingOrchestrationMessageBatches.AddLast(targetBatch);
   
                           // Before the batch of messages can be processed, we need to download the latest execution state.
                           // This is done beforehand in the background as a performance optimization.
                           Task.Run(() => this.ScheduleOrchestrationStatePrefetch(node, traceActivityId, cancellationToken));
                       }
             }
           }
   ```

   

而这个 AddMessageToPendingOrchestration()  方法被 DequeueLoop() 方法调用：

```c#
        async Task DequeueLoop(string partitionId, ControlQueue controlQueue, CancellationToken cancellationToken)
        {
          ......
            // Every dequeue operation has a common trace ID so that batches of dequeued messages can be correlated together.
                    // Both the dequeue traces and the processing traces will share the same "related" trace activity ID.
                    Guid traceActivityId = AzureStorageOrchestrationService.StartNewLogicalTraceScope(useExisting: false);

                    // This will block until either new messages arrive or the queue is released.
          // 从 command queue 中获取 messages
                    IReadOnlyList<MessageData> messages = await controlQueue.GetMessagesAsync(cancellationToken);
                    if (messages.Count > 0)
                    {
                        // De-dupe any execution started messages
                        // 对任何已开始执行的信息进行去磁（De-dupe）
                        IEnumerable<MessageData> filteredMessages = await this.DedupeExecutionStartedMessagesAsync(
                            controlQueue,
                            messages,
                            traceActivityId,
                            cancellationToken);

                        this.AddMessageToPendingOrchestration(controlQueue, filteredMessages, traceActivityId, cancellationToken);
                    }
          ......
        }
```





## 总结


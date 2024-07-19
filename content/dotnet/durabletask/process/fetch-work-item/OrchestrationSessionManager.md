---
title: "OrchestrationSessionManager"
linkTitle: "OrchestrationSessionManager"
weight: 20
date: 2024-01-19
description: >
  OrchestrationSessionManager
---



`src\DurableTask.AzureStorage\OrchestrationSessionManager.cs` 



```c#
    class OrchestrationSessionManager : IDisposable
    {
        readonly Dictionary<string, OrchestrationSession> activeOrchestrationSessions = new Dictionary<string, OrchestrationSession>(StringComparer.OrdinalIgnoreCase);
        readonly ConcurrentDictionary<string, ControlQueue> ownedControlQueues = new ConcurrentDictionary<string, ControlQueue>();
        readonly LinkedList<PendingMessageBatch> pendingOrchestrationMessageBatches = new LinkedList<PendingMessageBatch>();
        readonly AsyncQueue<LinkedListNode<PendingMessageBatch>> orchestrationsReadyForProcessingQueue = new AsyncQueue<LinkedListNode<PendingMessageBatch>>();
        readonly AsyncQueue<LinkedListNode<PendingMessageBatch>> entitiesReadyForProcessingQueue = new AsyncQueue<LinkedListNode<PendingMessageBatch>>();
    }
```



### activeOrchestrationSessions 

activeOrchestrationSessions 是一个 Dictionary

```c#
        readonly Dictionary<string, OrchestrationSession> activeOrchestrationSessions = new Dictionary<string, OrchestrationSession>(StringComparer.OrdinalIgnoreCase);

```



```c#
        public bool IsControlQueueProcessingMessages(string partitionId)
        {
            return this.activeOrchestrationSessions.Values.Where(session => string.Equals(session.ControlQueue.Name, partitionId)).Any();
        }
```



### 构造函数



```c#
        public OrchestrationSessionManager(
            string queueAccountName,
            AzureStorageOrchestrationServiceSettings settings,
            AzureStorageOrchestrationServiceStats stats,
            ITrackingStore trackingStore)
        {
            this.storageAccountName = queueAccountName;
            this.settings = settings;
            this.stats = stats;
            this.trackingStore = trackingStore;

            this.fetchRuntimeStateQueue = new DispatchQueue(this.settings.MaxStorageOperationConcurrency);
        }
```



## ControlQueue 相关的代码

拥有的 ControlQueue，类型为 ConcurrentDictionary，这是要保存多个 ControlQueue：

```c#
readonly ConcurrentDictionary<string, ControlQueue> ownedControlQueues = new ConcurrentDictionary<string, ControlQueue>();
```

key 是 partitionId。

queus 方法返回 ownedControlQueues 的值：

```c#
internal IEnumerable<ControlQueue> Queues => this.ownedControlQueues.Values;
```

### AddQueue()

```c#
        public void AddQueue(string partitionId, ControlQueue controlQueue, CancellationToken cancellationToken)
        {
            if (this.ownedControlQueues.TryAdd(partitionId, controlQueue))
            {
                _ = Task.Run(() => this.DequeueLoop(partitionId, controlQueue, cancellationToken));
            }
            else
            {
                this.settings.Logger.PartitionManagerWarning(
                    this.storageAccountName,
                    this.settings.TaskHubName,
                    this.settings.WorkerId,
                    partitionId,
                    $"Attempted to add a control queue {controlQueue.Name} multiple times!");
            }
        }
```

加入成功后，执行 DequeueLoop() 方法：

```c#
        async Task DequeueLoop(string partitionId, ControlQueue controlQueue, CancellationToken cancellationToken)
        {
            this.settings.Logger.PartitionManagerInfo(
                this.storageAccountName,
                this.settings.TaskHubName,
                this.settings.WorkerId,
                partitionId,
                $"Started listening for messages on queue {controlQueue.Name}.");

            while (!controlQueue.IsReleased)
            {
                try
                {
                    // Every dequeue operation has a common trace ID so that batches of dequeued messages can be correlated together.
                    // Both the dequeue traces and the processing traces will share the same "related" trace activity ID.
                    Guid traceActivityId = AzureStorageOrchestrationService.StartNewLogicalTraceScope(useExisting: false);

                    // This will block until either new messages arrive or the queue is released.
                    IReadOnlyList<MessageData> messages = await controlQueue.GetMessagesAsync(cancellationToken);
                    if (messages.Count > 0)
                    {
                        // De-dupe any execution started messages
                        IEnumerable<MessageData> filteredMessages = await this.DedupeExecutionStartedMessagesAsync(
                            controlQueue,
                            messages,
                            traceActivityId,
                            cancellationToken);

                        this.AddMessageToPendingOrchestration(controlQueue, filteredMessages, traceActivityId, cancellationToken);
                    }
                }
                catch (OperationCanceledException) when (cancellationToken.IsCancellationRequested)
                {
                    // shutting down
                    break;
                }
                catch (Exception e)
                {
                    this.settings.Logger.PartitionManagerWarning(
                        this.storageAccountName,
                        this.settings.TaskHubName,
                        this.settings.WorkerId,
                        partitionId,
                        $"Exception in the dequeue loop for control queue {controlQueue.Name}. Exception: {e}");

                    Thread.Sleep(TimeSpan.FromSeconds(1));
                }
            }
            
            this.settings.Logger.PartitionManagerInfo(
                this.storageAccountName,
                this.settings.TaskHubName,
                this.settings.WorkerId,
                partitionId,
                $"Stopped listening for messages on queue {controlQueue.Name}.");
        }
```

### RemoveQueue

```c#
        public void RemoveQueue(string partitionId, CloseReason? reason, string caller)
        {
            if (this.ownedControlQueues.TryRemove(partitionId, out ControlQueue controlQueue))
            {
                controlQueue.Release(reason, caller);
            }
        }
```

### ReleaseQueue()

```c#
        public void ReleaseQueue(string partitionId, CloseReason? reason, string caller)
        {
            if (this.ownedControlQueues.TryGetValue(partitionId, out ControlQueue controlQueue))
            {
                controlQueue.Release(reason, caller);
            }
        }
```

### IsControlQueueReceivingMessages()

```c#
        public bool IsControlQueueReceivingMessages(string partitionId)
        {
            return this.ownedControlQueues.TryGetValue(partitionId, out ControlQueue controlQueue)
                && !controlQueue.IsReleased;
        }
```

### IsControlQueueProcessingMessages()

```c#
        public bool IsControlQueueProcessingMessages(string partitionId)
        {
            return this.activeOrchestrationSessions.Values.Where(session => string.Equals(session.ControlQueue.Name, partitionId)).Any();
        }
```

## 需要仔细理解的方法



### GetNextSessionAsync()



```c#
       public async Task<OrchestrationSession?> GetNextSessionAsync(bool entitiesOnly, CancellationToken cancellationToken)
        {
            var readyForProcessingQueue = entitiesOnly? this.entitiesReadyForProcessingQueue : this.orchestrationsReadyForProcessingQueue;

            while (!cancellationToken.IsCancellationRequested)
            {
                // This call will block until:
                //  1) a batch of messages has been received for a particular instance and
                //  2) the history for that instance has been fetched
                LinkedListNode<PendingMessageBatch> node = await readyForProcessingQueue.DequeueAsync(cancellationToken);

                lock (this.messageAndSessionLock)
                {
                    PendingMessageBatch nextBatch = node.Value;
                    this.pendingOrchestrationMessageBatches.Remove(node);

                    if (!this.activeOrchestrationSessions.TryGetValue(nextBatch.OrchestrationInstanceId, out var existingSession))
                    {
                        OrchestrationInstance instance = nextBatch.OrchestrationState?.OrchestrationInstance ??
                            new OrchestrationInstance
                            {
                                InstanceId = nextBatch.OrchestrationInstanceId,
                                ExecutionId = nextBatch.OrchestrationExecutionId,
                            };

                        // 如果 activeOrchestrationSessions 中不存在，则在 AzureStorage 中 trace
                        Guid traceActivityId = AzureStorageOrchestrationService.StartNewLogicalTraceScope(useExisting: true);

                        OrchestrationSession session = new OrchestrationSession(
                            this.settings,
                            this.storageAccountName,
                            instance,
                            nextBatch.ControlQueue,
                            nextBatch.Messages,
                            nextBatch.OrchestrationState,
                            nextBatch.ETag,
                            nextBatch.LastCheckpointTime,
                            nextBatch.TrackingStoreContext,
                            this.settings.ExtendedSessionIdleTimeout,
                            traceActivityId);

                        // 然后创建一个 session 保存到 activeOrchestrationSessions 中
                        // 这样就保证了 activeOrchestrationSessions 中的 session 都是被 AzureStorage trace 的
                        this.activeOrchestrationSessions.Add(instance.InstanceId, session);

                        return session;
                    }
                    else if (nextBatch.OrchestrationExecutionId == existingSession.Instance?.ExecutionId)
                    {
                        // there is already an active session with the same execution id.
                        // The session might be waiting for more messages. If it is, signal them.
                        // 已经有一个具有相同执行 ID 的活动会话 
                        // 该会话可能正在等待更多信息。 如果是，则发出信号。
                        existingSession.AddOrReplaceMessages(node.Value.Messages);
                    }
                    else
                    {
                        // A message arrived for a different generation of an existing orchestration instance.
                        // Put it back into the ready queue so that it can be processed once the current generation
                        // is done executing.
                      // 为现有协调实例的另一代发送消息。
                      // 将其放回就绪队列，以便在当前一代执行完毕后对其进行处理。
                        if (readyForProcessingQueue.Count == 0)
                        {
                            // To avoid a tight dequeue loop, delay for a bit before putting this node back into the queue.
                            // This is only necessary when the queue is empty. The main dequeue thread must not be blocked
                            // by this delay, which is why we use Task.Delay(...).ContinueWith(...) instead of await.
                            Task.Delay(millisecondsDelay: 200).ContinueWith(_ =>
                            {
                                lock (this.messageAndSessionLock)
                                {
                                    this.pendingOrchestrationMessageBatches.AddLast(node);
                                    readyForProcessingQueue.Enqueue(node);
                                }
                            });
                        }
                        else
                        {
                            this.pendingOrchestrationMessageBatches.AddLast(node);
                            readyForProcessingQueue.Enqueue(node);
                        }
                    }
                }
            }

            return null;
        }
```

### TryGetExistingSession()

```c#
        public bool TryGetExistingSession(string instanceId, out OrchestrationSession session)
        {
            lock (this.messageAndSessionLock)
            {
                return this.activeOrchestrationSessions.TryGetValue(instanceId, out session);
            }
        }
```





## 暂时先不深入的方法



### AddMessageToPendingOrchestration

Adds history messages to an orchestration for its next replay. "Pending" here is unrelated to the Pending runtimeStatus.

为协调的下一次重放添加历史消息。这里的 "待处理 "与运行时状态 "待处理 "无关。

```c#
        internal void AddMessageToPendingOrchestration(
            ControlQueue controlQueue,
            IEnumerable<MessageData> queueMessages,
            Guid traceActivityId,
            CancellationToken cancellationToken)
        {
            // Conditions to consider:
            //  1. Do we need to create a new orchestration session or does one already exist?
            //  2. Do we already have a copy of this message?
            //  3. Do we need to add messages to a currently executing orchestration?
            lock (this.messageAndSessionLock)
            {
                var existingSessionMessages = new Dictionary<OrchestrationSession, List<MessageData>>();

                foreach (MessageData data in queueMessages)
                {
                    // The instanceID identifies the orchestration across replays and ContinueAsNew generations.
                    // The executionID identifies a generation of an orchestration instance, doesn't change across replays.
                    string instanceId = data.TaskMessage.OrchestrationInstance.InstanceId;
                    string executionId = data.TaskMessage.OrchestrationInstance.ExecutionId;

                    // If the target orchestration is already in memory, we can potentially add the message to the session directly
                    // rather than adding it to the pending list. This behavior applies primarily when extended sessions are enabled.
                    // We can't do this for ExecutionStarted messages - those must *always* go to the pending list since they are for
                    // creating entirely new orchestration instances.
                    if (data.TaskMessage.Event.EventType != EventType.ExecutionStarted &&
                        this.activeOrchestrationSessions.TryGetValue(instanceId, out OrchestrationSession session))
                    {
                        // A null executionId value means that this is a management operation, like RaiseEvent or Terminate, which
                        // should be delivered to the current session.
                        if (executionId == null || session.Instance.ExecutionId == executionId)
                        {
                            List<MessageData> pendingMessages;
                            if (!existingSessionMessages.TryGetValue(session, out pendingMessages))
                            {
                                pendingMessages = new List<MessageData>();
                                existingSessionMessages.Add(session, pendingMessages);
                            }

                            pendingMessages.Add(data);
                            continue;
                        }

                        // Looks like this message is for another generation of the active orchestration. Let it fall
                        // into the pending list below. If it's a message for an older generation, it will be eventually
                        // discarded after we discover that we have no state associated with its execution ID. This is
                        // most common in scenarios involving durable timers and ContinueAsNew. Otherwise, this message
                        // will be processed after the current session unloads.
                    }

                    PendingMessageBatch? targetBatch = null; // batch for the current instanceID-executionID pair

                    // Unless the message is an ExecutionStarted event, we attempt to assign the current message to an
                    // existing batch by walking backwards through the list of batches until we find one with a matching InstanceID.
                    // This is assumed to be more efficient than walking forward if most messages arrive in the queue in groups.
                    LinkedListNode<PendingMessageBatch> node = this.pendingOrchestrationMessageBatches.Last;
                    while (node != null && data.TaskMessage.Event.EventType != EventType.ExecutionStarted)
                    {
                        PendingMessageBatch batch = node.Value;

                        if (batch.OrchestrationInstanceId == instanceId)
                        {
                            if (executionId == null || batch.OrchestrationExecutionId == executionId)
                            {
                                targetBatch = batch;
                                break;
                            }
                            else if (batch.OrchestrationExecutionId == null)
                            {
                                targetBatch = batch;
                                batch.OrchestrationExecutionId = executionId;
                                break;
                            }
                        }

                        node = node.Previous;
                    }

                    // If there is no batch for this instanceID-executionID pair, create one
                    if (targetBatch == null)
                    {
                        targetBatch = new PendingMessageBatch(controlQueue, instanceId, executionId);
                        node = this.pendingOrchestrationMessageBatches.AddLast(targetBatch);

                        // Before the batch of messages can be processed, we need to download the latest execution state.
                        // This is done beforehand in the background as a performance optimization.
                        Task.Run(() => this.ScheduleOrchestrationStatePrefetch(node, traceActivityId, cancellationToken));
                    }

                    // New messages are added; duplicate messages are replaced
                    targetBatch.Messages.AddOrReplace(data);
                }

                // The session might be waiting for more messages. If it is, signal them.
                foreach (var pair in existingSessionMessages)
                {
                    OrchestrationSession session = pair.Key;
                    List<MessageData> newMessages = pair.Value;

                    // New messages are added; duplicate messages are replaced
                    session.AddOrReplaceMessages(newMessages);
                }
            }
        }
```







## 其他方法

### DrainAsync()

The drain process occurs when the lease is stolen or the worker is shutting down, prompting the worker to cease listening for new messages and to finish processing all the existing information in memory.

当租约被盗或 Worker 即将关闭时，就会发生泄密过程，促使 Worker 停止监听新信息，并完成对内存中所有现有信息的处理。



### DedupeExecutionStartedMessagesAsync()



This method enumerates all the provided queue messages looking for ExecutionStarted messages. If any are found, it queries table storage to ensure that each message has a matching record in the Instances table. If not, this method will either asynchronously discard the message or abandon it for reprocessing in case the Instances table record hasn't been written yet (this happens asynchronously and there is no guaranteed order). Meanwhile, this method will return the list of filtered messages.

此方法会枚举所有提供的队列消息，查找 ExecutionStarted 消息。如果找到，它将查询表存储，确保每条消息在实例表中都有匹配记录。如果没有，该方法会异步丢弃消息，或者在实例表记录尚未写入的情况下放弃消息以便重新处理（这是异步发生的，没有顺序保证）。同时，该方法将返回已过滤的消息列表。

DedupeExecutionStartedMessagesAsync




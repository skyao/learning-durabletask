---
title: "AzureStorageOrchestrationService"
linkTitle: "AzureStorageOrchestrationService"
weight: 30
date: 2024-01-19
description: >
  AzureStorageOrchestrationService
---



`src\DurableTask.AzureStorage\AzureStorageOrchestrationService.cs` 



```c#
    public sealed class AzureStorageOrchestrationService :
        IOrchestrationService,
        IOrchestrationServiceClient,
        IDisposable, 
        IOrchestrationServiceQueryClient,
        IOrchestrationServicePurgeClient,
        IEntityOrchestrationService
    {
```



### 构造函数



```c#
public AzureStorageOrchestrationService(AzureStorageOrchestrationServiceSettings settings, IOrchestrationServiceInstanceStore customInstanceStore)
        {
            if (settings == null)
            {
                throw new ArgumentNullException(nameof(settings));
            }

            ValidateSettings(settings);

            this.settings = settings;

            this.azureStorageClient = new AzureStorageClient(settings);
            this.stats = this.azureStorageClient.Stats;

  					// “sampleshub-largemessages”
            string compressedMessageBlobContainerName = $"{settings.TaskHubName.ToLowerInvariant()}-largemessages";
            this.messageManager = new MessageManager(this.settings, this.azureStorageClient, compressedMessageBlobContainerName);

            // 初始化 allControlQueues
            this.allControlQueues = new ConcurrentDictionary<string, ControlQueue>();
            for (int index = 0; index < this.settings.PartitionCount; index++)
            {
                // 分区初始化controlQueue，然后加入 allControlQueues
              // controlQueueName = “sampleshub-control-00”
                var controlQueueName = GetControlQueueName(this.settings.TaskHubName, index);
                ControlQueue controlQueue = new ControlQueue(this.azureStorageClient, controlQueueName, this.messageManager);
                this.allControlQueues.TryAdd(controlQueue.Name, controlQueue);
            }

  // workItemQueueName = “sampleshub-workitems”
            var workItemQueueName = GetWorkItemQueueName(this.settings.TaskHubName);
            this.workItemQueue = new WorkItemQueue(this.azureStorageClient, workItemQueueName, this.messageManager);

            if (customInstanceStore == null)
            { 
                // 默认用 AzureTableTrackingStore
                this.trackingStore = new AzureTableTrackingStore(this.azureStorageClient, this.messageManager);
            }
            else
            {
                this.trackingStore = new InstanceStoreBackedTrackingStore(customInstanceStore);
            }

            this.activeActivitySessions = new ConcurrentDictionary<string, ActivitySession>(StringComparer.OrdinalIgnoreCase);

            this.hubCreationLock = new object();
            this.taskHubCreator = new ResettableLazy<Task>(
                this.GetTaskHubCreatorTask,
                LazyThreadSafetyMode.ExecutionAndPublication);

            this.leaseManager = GetBlobLeaseManager(
                this.azureStorageClient,
                "default");

            this.orchestrationSessionManager = new OrchestrationSessionManager(
                this.azureStorageClient.QueueAccountName,
                this.settings,
                this.stats,
                this.trackingStore);

            if (this.settings.UseTablePartitionManagement && this.settings.UseLegacyPartitionManagement)
            {
                throw new ArgumentException("Cannot use both TablePartitionManagement and LegacyPartitionManagement. For improved reliability, consider using the TablePartitionManager.");  
            }
            else if (this.settings.UseTablePartitionManagement)
            {
                this.partitionManager = new TablePartitionManager(
                    this,
                    this.azureStorageClient);
            }
            else if (this.settings.UseLegacyPartitionManagement)
            {
                this.partitionManager = new LegacyPartitionManager(
                    this,
                    this.azureStorageClient);
            }
            else
            {
                this.partitionManager = new SafePartitionManager(
                    this,
                    this.azureStorageClient,
                    this.orchestrationSessionManager);
            }

            this.appLeaseManager = new AppLeaseManager(
                this.azureStorageClient,
                this.partitionManager,
                this.settings.TaskHubName.ToLowerInvariant() + "-applease",
                this.settings.TaskHubName.ToLowerInvariant() + "-appleaseinfo",
                this.settings.AppLeaseOptions);
        }
```



### GetControlQueuesAsync()



```c#
internal static async Task<Queue[]> GetControlQueuesAsync(
            AzureStorageClient azureStorageClient,
            int defaultPartitionCount)
        {
            if (azureStorageClient == null)
            {
                throw new ArgumentNullException(nameof(azureStorageClient));
            }

            string taskHub = azureStorageClient.Settings.TaskHubName;

            // Need to check for leases in Azure Table Storage. Scale Controller calls into this method.
            int partitionCount;
            Table partitionTable = azureStorageClient.GetTableReference(azureStorageClient.Settings.PartitionTableName);
            
            // Check if table partition manager is used. If so, get partition count from table.
            // Else, get the partition count from the blobs.
            if (await partitionTable.ExistsAsync())
            {
                TableEntitiesResponseInfo<DynamicTableEntity> result = await partitionTable.ExecuteQueryAsync(new TableQuery<DynamicTableEntity>());
                partitionCount = result.ReturnedEntities.Count;
            }
            else
            {
                BlobLeaseManager inactiveLeaseManager = GetBlobLeaseManager(azureStorageClient, "inactive");

                TaskHubInfo hubInfo = await inactiveLeaseManager.GetOrCreateTaskHubInfoAsync(
                    GetTaskHubInfo(taskHub, defaultPartitionCount),
                    checkIfStale: false);
                partitionCount = hubInfo.PartitionCount;
            };

            var controlQueues = new Queue[partitionCount];
            for (int i = 0; i < partitionCount; i++)
            {
                controlQueues[i] = azureStorageClient.GetQueueReference(GetControlQueueName(taskHub, i));
            }

            return controlQueues;
        }
```



### GetWorkItemQueue()

```c#
        internal static Queue GetWorkItemQueue(AzureStorageClient azureStorageClient)
        {
            string queueName = GetWorkItemQueueName(azureStorageClient.Settings.TaskHubName);
            return azureStorageClient.GetQueueReference(queueName);
        }
```





### LockNextTaskOrchestrationWorkItemAsync() 



```c#
        public Task<TaskOrchestrationWorkItem> LockNextTaskOrchestrationWorkItemAsync(
            TimeSpan receiveTimeout,
            CancellationToken cancellationToken)
        {

            return LockNextTaskOrchestrationWorkItemAsync(entitiesOnly: false, cancellationToken);
        }
```

LockNextTaskOrchestrationWorkItemAsync()

```c#

        async Task<TaskOrchestrationWorkItem> LockNextTaskOrchestrationWorkItemAsync(bool entitiesOnly, CancellationToken cancellationToken)
        {
            Guid traceActivityId = StartNewLogicalTraceScope(useExisting: true);

            await this.EnsureTaskHubAsync();

            using (var linkedCts = CancellationTokenSource.CreateLinkedTokenSource(cancellationToken, this.shutdownSource.Token))
            {
                OrchestrationSession session = null;
                TaskOrchestrationWorkItem orchestrationWorkItem = null;

                try
                {
                    // This call will block until the next session is ready
                    // 获得下一个 session，关键在这里
                    session = await this.orchestrationSessionManager.GetNextSessionAsync(entitiesOnly, linkedCts.Token);
                    if (session == null)
                    {
                        return null;
                    }

                    // Make sure we still own the partition. If not, abandon the session.
                    if (session.ControlQueue.IsReleased)
                    {
                        await this.AbandonAndReleaseSessionAsync(session);
                        return null;
                    }

                    session.StartNewLogicalTraceScope();

                    List<MessageData> outOfOrderMessages = null;

                    foreach (MessageData message in session.CurrentMessageBatch)
                    {
                        if (session.IsOutOfOrderMessage(message))
                        {
                            if (outOfOrderMessages == null)
                            {
                                outOfOrderMessages = new List<MessageData>();
                            }

                            // This can happen if a lease change occurs and a new node receives a message for an 
                            // orchestration that has not yet checkpointed its history. We abandon such messages
                            // so that they can be reprocessed after the history checkpoint has completed.
                            this.settings.Logger.ReceivedOutOfOrderMessage(
                                this.azureStorageClient.QueueAccountName,
                                this.settings.TaskHubName,
                                session.Instance.InstanceId,
                                session.Instance.ExecutionId,
                                session.ControlQueue.Name,
                                message.TaskMessage.Event.EventType.ToString(),
                                Utils.GetTaskEventId(message.TaskMessage.Event),
                                message.OriginalQueueMessage.Id,
                                message.Episode.GetValueOrDefault(-1),
                                session.LastCheckpointTime);
                            outOfOrderMessages.Add(message);
                        }
                        else
                        {
                            session.TraceProcessingMessage(message, isExtendedSession: false);
                        }
                    }

                    if (outOfOrderMessages?.Count > 0)
                    {
                        // This will also remove the messages from the current batch.
                        await this.AbandonMessagesAsync(session, outOfOrderMessages);
                    }

                    if (session.CurrentMessageBatch.Count == 0)
                    {
                        // All messages were removed. Release the work item.
                        await this.AbandonAndReleaseSessionAsync(session);
                        return null;
                    }

                    // Create or restore Correlation TraceContext

                    TraceContextBase currentRequestTraceContext = null;
                    CorrelationTraceClient.Propagate(
                        () =>
                        {
                            var isReplaying = session.RuntimeState.ExecutionStartedEvent?.IsPlayed ?? false;
                            TraceContextBase parentTraceContext = GetParentTraceContext(session);
                            currentRequestTraceContext = GetRequestTraceContext(isReplaying, parentTraceContext);
                        });


                   // 创建 orchestrationWorkItem
                    orchestrationWorkItem = new TaskOrchestrationWorkItem
                    {
                        InstanceId = session.Instance.InstanceId,
                        LockedUntilUtc = session.CurrentMessageBatch.Min(msg => msg.OriginalQueueMessage.NextVisibleTime.Value.UtcDateTime),
                        NewMessages = session.CurrentMessageBatch.Select(m => m.TaskMessage).ToList(),
                        OrchestrationRuntimeState = session.RuntimeState,
                        Session = this.settings.ExtendedSessionsEnabled ? session : null,
                        TraceContext = currentRequestTraceContext,
                    };
                    

                    if (!this.IsExecutableInstance(session.RuntimeState, orchestrationWorkItem.NewMessages, out string warningMessage))
                    {
                        // If all messages belong to the same execution ID, then all of them need to be discarded.
                        // However, it's also possible to have messages for *any* execution ID batched together with messages
                        // to a *specific* (non-executable) execution ID. Those messages should *not* be discarded since
                        // they might be consumable by another orchestration with the same instance id but different execution ID.
                        var messagesToDiscard = new List<MessageData>();
                        var messagesToAbandon = new List<MessageData>();
                        foreach (MessageData msg in session.CurrentMessageBatch)
                        {
                            if (msg.TaskMessage.OrchestrationInstance.ExecutionId == session.Instance.ExecutionId)
                            {
                                messagesToDiscard.Add(msg);
                            }
                            else
                            {
                                messagesToAbandon.Add(msg);
                            }
                        }

                        // If no messages have a matching execution ID, then delete all of them. This means all the
                        // messages are external (external events, termination, etc.) and were sent to an instance that
                        // doesn't exist or is no longer in a running state.
                        if (messagesToDiscard.Count == 0)
                        {
                            messagesToDiscard.AddRange(messagesToAbandon);
                            messagesToAbandon.Clear();
                        }

                        // Add all abandoned messages to the deferred list. These messages will not be deleted right now.
                        // If they can be matched with another orchestration, then great. Otherwise they will be deleted
                        // the next time they are picked up.
                        messagesToAbandon.ForEach(session.DeferMessage);

                        var eventListBuilder = new StringBuilder(orchestrationWorkItem.NewMessages.Count * 40);
                        foreach (MessageData msg in messagesToDiscard)
                        {
                            eventListBuilder.Append(msg.TaskMessage.Event.EventType.ToString()).Append(',');
                        }

                        this.settings.Logger.DiscardingWorkItem(
                            this.azureStorageClient.QueueAccountName,
                            this.settings.TaskHubName,
                            session.Instance.InstanceId,
                            session.Instance.ExecutionId,
                            orchestrationWorkItem.NewMessages.Count,
                            session.RuntimeState.Events.Count,
                            eventListBuilder.ToString(0, eventListBuilder.Length - 1) /* remove trailing comma */,
                            warningMessage);

                        // The instance has already completed or never existed. Delete this message batch.
                        await this.DeleteMessageBatchAsync(session, messagesToDiscard);
                        await this.ReleaseTaskOrchestrationWorkItemAsync(orchestrationWorkItem);
                        return null;
                    }

                    System.Console.WriteLine("********* versioning  ************* LockNextTaskOrchestrationWorkItemAsync()");
                    //Console.WriteLine("********* versioning  *************: orchestrationWorkItem: name=" + orchestrationWorkItem.OrchestrationRuntimeState!.Name + ", version=" + orchestrationWorkItem.OrchestrationRuntimeState!.Version);
                    return orchestrationWorkItem;
                }
                catch (OperationCanceledException)
                {
                    if (session != null)
                    {
                        // host is shutting down - release any queued messages
                        await this.AbandonAndReleaseSessionAsync(session);
                    }

                    return null;
                }
                catch (Exception e)
                {

                    // print out the exception
                    Console.WriteLine("********* versioning  ************* LockNextTaskOrchestrationWorkItemAsync() exception: " + e.ToString());

                    this.settings.Logger.OrchestrationProcessingFailure(
                        this.azureStorageClient.QueueAccountName,
                        this.settings.TaskHubName,
                        session?.Instance.InstanceId ?? string.Empty,
                        session?.Instance.ExecutionId ?? string.Empty,
                        e.ToString());

                    if (orchestrationWorkItem != null)
                    {
                        // The work-item needs to be released so that it can be retried later.
                        await this.ReleaseTaskOrchestrationWorkItemAsync(orchestrationWorkItem);
                    }

                    throw;
                }
            }
        }
```


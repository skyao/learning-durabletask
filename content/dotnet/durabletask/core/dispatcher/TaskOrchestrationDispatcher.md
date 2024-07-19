---
title: "TaskOrchestrationDispatcher源码学习"
linkTitle: "TaskOrchestrationDispatcher"
weight: 10
date: 2024-01-19
description: >
  学习durabletask TaskOrchestrationDispatcher的源码
---



Dispatcher for orchestrations to handle processing and renewing, completion of orchestration events

协调的调度程序，用于处理、更新和完成协调事件

`src\DurableTask.Core\TaskOrchestrationDispatcher.cs`



## 类定义

没任何借口或者基类：

```c#
    public class TaskOrchestrationDispatcher{}
```



### 类字段



```c#
        static readonly Task CompletedTask = Task.FromResult(0);

        readonly INameVersionObjectManager<TaskOrchestration> objectManager;
        readonly IOrchestrationService orchestrationService;
        readonly WorkItemDispatcher<TaskOrchestrationWorkItem> dispatcher;
        readonly DispatchMiddlewarePipeline dispatchPipeline;
        readonly LogHelper logHelper;
        ErrorPropagationMode errorPropagationMode;
        readonly NonBlockingCountdownLock concurrentSessionLock;
        readonly IEntityOrchestrationService? entityOrchestrationService;
        readonly EntityBackendProperties? entityBackendProperties;
        readonly TaskOrchestrationEntityParameters? entityParameters;
```

主要的功能在 objectManager ， orchestrationService ， dispatcher 和 DispatchMiddlewarePipeline。



### 构造函数



```c#
internal TaskOrchestrationDispatcher(
            IOrchestrationService orchestrationService,
            INameVersionObjectManager<TaskOrchestration> objectManager,
            DispatchMiddlewarePipeline dispatchPipeline,
            LogHelper logHelper,
            ErrorPropagationMode errorPropagationMode)
        {
            // 这几个字段都是传递进来的
            this.objectManager = objectManager ?? throw new ArgumentNullException(nameof(objectManager));
            this.orchestrationService = orchestrationService ?? throw new ArgumentNullException(nameof(orchestrationService));
            this.dispatchPipeline = dispatchPipeline ?? throw new ArgumentNullException(nameof(dispatchPipeline));
            this.logHelper = logHelper ?? throw new ArgumentNullException(nameof(logHelper));
            this.errorPropagationMode = errorPropagationMode;
  
            // orchestrationService 转为 IEntityOrchestrationService
            this.entityOrchestrationService = orchestrationService as IEntityOrchestrationService;
            this.entityBackendProperties = this.entityOrchestrationService?.EntityBackendProperties;
            this.entityParameters = TaskOrchestrationEntityParameters.FromEntityBackendProperties(this.entityBackendProperties);

  					// 初始化 dispatcher
            this.dispatcher = new WorkItemDispatcher<TaskOrchestrationWorkItem>(
                "TaskOrchestrationDispatcher",
                item => item == null ? string.Empty : item.InstanceId,
                this.OnFetchWorkItemAsync,
                this.OnProcessWorkItemSessionAsync)
            {
                // 初始化 dispatcher 的字段
                GetDelayInSecondsAfterOnFetchException = orchestrationService.GetDelayInSecondsAfterOnFetchException,
                GetDelayInSecondsAfterOnProcessException = orchestrationService.GetDelayInSecondsAfterOnProcessException,
                SafeReleaseWorkItem = orchestrationService.ReleaseTaskOrchestrationWorkItemAsync,
                AbortWorkItem = orchestrationService.AbandonTaskOrchestrationWorkItemAsync,
                DispatcherCount = orchestrationService.TaskOrchestrationDispatcherCount,
                MaxConcurrentWorkItems = orchestrationService.MaxConcurrentTaskOrchestrationWorkItems,
                LogHelper = logHelper,
            };

            // To avoid starvation, we only allow half of all concurrently execution orchestrations to
            // leverage extended sessions.
            var maxConcurrentSessions = (int)Math.Ceiling(this.dispatcher.MaxConcurrentWorkItems / 2.0);
            this.concurrentSessionLock = new NonBlockingCountdownLock(maxConcurrentSessions);
        }
```



## StartAsync()  方法

Starts the dispatcher to start getting and processing orchestration events

启动调度程序，开始获取和处理协调事件

```c#
        public async Task StartAsync()
        {
            await this.dispatcher.StartAsync();
        }
```



### OnFetchWorkItemAsync() 方法

Method to get the next work item to process within supplied timeout

在提供的超时时间内获取下一个要处理的工作项的方法

```c#
        protected Task<TaskOrchestrationWorkItem> OnFetchWorkItemAsync(TimeSpan receiveTimeout, CancellationToken cancellationToken)
        {
            if (this.entityBackendProperties?.UseSeparateQueueForEntityWorkItems == true)
            {
                // only orchestrations should be served by this dispatcher, so we call
                // the method which returns work items for orchestrations only.
                // 该调度器只应为协调提供服务，因此我们调用这个方法，该方法只返回协调的工作项。
                Console.WriteLine("OnFetchWorkItemAsync: UseSeparateQueueForEntityWorkItems == true");
                Console.WriteLine("OnFetchWorkItemAsync: this.entityOrchestrationService=" + this.entityOrchestrationService?.GetType().FullName);
                return this.entityOrchestrationService!.LockNextOrchestrationWorkItemAsync(receiveTimeout, cancellationToken);
            }
            else
            {
                // both entities and orchestrations are served by this dispatcher,
                // so we call the method that may return work items for either.
                // 实体和编排都由该调度器提供服务，因此我们调用的方法可能会返回两者的工作项。
                return this.orchestrationService.LockNextTaskOrchestrationWorkItemAsync(receiveTimeout, cancellationToken);
            }
        }
```

增加日志打印，看到：

```bash
OnFetchWorkItemAsync: UseSeparateQueueForEntityWorkItems == true
OnFetchWorkItemAsync: this.entityOrchestrationService=Microsoft.Azure.WebJobs.Extensions.DurableTask.AzureStorageDurabilityProvider
```





### OnProcessWorkItemSessionAsync() 方法



```c#
async Task OnProcessWorkItemSessionAsync(TaskOrchestrationWorkItem workItem)
        {
            // DTFx history replay expects that ExecutionStarted comes before other events.
            // If this is not already the case, due to a race-condition, we re-order the
            // messages to enforce this expectation.
            EnsureExecutionStartedIsFirst(workItem.NewMessages);

            try
            {
                if (workItem.Session == null)
                {
                    // Legacy behavior
                    await this.OnProcessWorkItemAsync(workItem);
                    return;
                }

                var isExtendedSession = false;

                CorrelationTraceClient.Propagate(
                    () =>
                    {                
                        // Check if it is extended session.
                        // TODO: Remove this code - it looks incorrect and dangerous
                        isExtendedSession = this.concurrentSessionLock.Acquire();
                        this.concurrentSessionLock.Release();
                        workItem.IsExtendedSession = isExtendedSession;
                    });

                var processCount = 0;
                try
                {
                    while (true)
                    {
                        // If the provider provided work items, execute them.
                        if (workItem.NewMessages?.Count > 0)
                        {
                            bool isCompletedOrInterrupted = await this.OnProcessWorkItemAsync(workItem);
                            if (isCompletedOrInterrupted)
                            {
                                break;
                            }

                            processCount++;
                        }

                        // Fetches beyond the first require getting an extended session lock, used to prevent starvation.
                        if (processCount > 0 && !isExtendedSession)
                        {
                            isExtendedSession = this.concurrentSessionLock.Acquire();
                            if (!isExtendedSession)
                            {
                                TraceHelper.Trace(TraceEventType.Verbose, "OnProcessWorkItemSession-MaxOperations", "Failed to acquire concurrent session lock.");
                                break;
                            }
                        }

                        TraceHelper.Trace(TraceEventType.Verbose, "OnProcessWorkItemSession-StartFetch", "Starting fetch of existing session.");
                        Stopwatch timer = Stopwatch.StartNew();

                        // Wait for new messages to arrive for the session. This call is expected to block (asynchronously)
                        // until either new messages are available or until a provider-specific timeout has expired.
                        workItem.NewMessages = await workItem.Session.FetchNewOrchestrationMessagesAsync(workItem);
                        if (workItem.NewMessages == null)
                        {
                            break;
                        }

                        TraceHelper.Trace(
                            TraceEventType.Verbose,
                            "OnProcessWorkItemSession-EndFetch",
                            $"Fetched {workItem.NewMessages.Count} new message(s) after {timer.ElapsedMilliseconds} ms from existing session.");
                        workItem.OrchestrationRuntimeState.NewEvents.Clear();
                    }
                }
                finally
                {
                    if (isExtendedSession)
                    {
                        TraceHelper.Trace(
                            TraceEventType.Verbose,
                            "OnProcessWorkItemSession-Release",
                            $"Releasing extended session after {processCount} batch(es).");
                        this.concurrentSessionLock.Release();
                    }
                }
            }
            catch (SessionAbortedException e)
            {
                // Either the orchestration or the orchestration service explicitly abandoned the session.
                OrchestrationInstance instance = workItem.OrchestrationRuntimeState?.OrchestrationInstance ?? new OrchestrationInstance { InstanceId = workItem.InstanceId };
                this.logHelper.OrchestrationAborted(instance, e.Message);
                TraceHelper.TraceInstance(TraceEventType.Warning, "TaskOrchestrationDispatcher-ExecutionAborted", instance, "{0}", e.Message);
                await this.orchestrationService.AbandonTaskOrchestrationWorkItemAsync(workItem);
            }
        }
```





OnProcessWorkItemAsync()



```c#
protected async Task<bool> OnProcessWorkItemAsync(TaskOrchestrationWorkItem workItem)
        {
            var messagesToSend = new List<TaskMessage>();
            var timerMessages = new List<TaskMessage>();
            var orchestratorMessages = new List<TaskMessage>();
            var isCompleted = false;
            var continuedAsNew = false;
            var isInterrupted = false;
            
            // correlation
            CorrelationTraceClient.Propagate(() => CorrelationTraceContext.Current = workItem.TraceContext);

            ExecutionStartedEvent? continueAsNewExecutionStarted = null;
            TaskMessage? continuedAsNewMessage = null;
            IList<HistoryEvent>? carryOverEvents = null;
            string? carryOverStatus = null;

            workItem.OrchestrationRuntimeState.LogHelper = this.logHelper;
            OrchestrationRuntimeState runtimeState = workItem.OrchestrationRuntimeState;

            runtimeState.AddEvent(new OrchestratorStartedEvent(-1));

            OrchestrationRuntimeState originalOrchestrationRuntimeState = runtimeState;

            // Distributed tracing support: each orchestration execution is a trace activity
            // that derives from an established parent trace context. It is expected that some
            // listener will receive these events and publish them to a distributed trace logger.
            ExecutionStartedEvent startEvent =
                runtimeState.ExecutionStartedEvent ??
                workItem.NewMessages.Select(msg => msg.Event).OfType<ExecutionStartedEvent>().FirstOrDefault();
            Activity? traceActivity = TraceHelper.StartTraceActivityForOrchestrationExecution(startEvent);

            OrchestrationState? instanceState = null;

            Task? renewTask = null;
            using var renewCancellationTokenSource = new CancellationTokenSource();
            if (workItem.LockedUntilUtc < DateTime.MaxValue)
            {
                // start a task to run RenewUntil
                renewTask = Task.Factory.StartNew(
                    () => RenewUntil(workItem, this.orchestrationService, this.logHelper, nameof(TaskOrchestrationDispatcher), renewCancellationTokenSource.Token),
                    renewCancellationTokenSource.Token);
            }

            try
            {
                // Assumes that: if the batch contains a new "ExecutionStarted" event, it is the first message in the batch.
                if (!ReconcileMessagesWithState(workItem, nameof(TaskOrchestrationDispatcher), this.errorPropagationMode, logHelper))
                {
                    // TODO : mark an orchestration as faulted if there is data corruption
                    this.logHelper.DroppingOrchestrationWorkItem(workItem, "Received work-item for an invalid orchestration");
                    TraceHelper.TraceSession(
                        TraceEventType.Error,
                        "TaskOrchestrationDispatcher-DeletedOrchestration",
                        runtimeState.OrchestrationInstance?.InstanceId!,
                        "Received work-item for an invalid orchestration");
                    isCompleted = true;
                    traceActivity?.Dispose();
                }
                else
                {
                    do
                    {
                        continuedAsNew = false;
                        continuedAsNewMessage = null;


                        this.logHelper.OrchestrationExecuting(runtimeState.OrchestrationInstance!, runtimeState.Name);
                        TraceHelper.TraceInstance(
                            TraceEventType.Verbose,
                            "TaskOrchestrationDispatcher-ExecuteUserOrchestration-Begin",
                            runtimeState.OrchestrationInstance!,
                            "Executing user orchestration: {0}",
                            JsonDataConverter.Default.Serialize(runtimeState.GetOrchestrationRuntimeStateDump(), true));

                        if (workItem.Cursor == null)
                        {
                            workItem.Cursor = await this.ExecuteOrchestrationAsync(runtimeState, workItem);
                        }
                        else
                        {
                            await this.ResumeOrchestrationAsync(workItem);
                        }

                        IReadOnlyList<OrchestratorAction> decisions = workItem.Cursor.LatestDecisions.ToList();

                        this.logHelper.OrchestrationExecuted(
                            runtimeState.OrchestrationInstance!,
                            runtimeState.Name,
                            decisions);
                        TraceHelper.TraceInstance(
                            TraceEventType.Information,
                            "TaskOrchestrationDispatcher-ExecuteUserOrchestration-End",
                            runtimeState.OrchestrationInstance!,
                            "Executed user orchestration. Received {0} orchestrator actions: {1}",
                            decisions.Count,
                            string.Join(", ", decisions.Select(d => d.Id + ":" + d.OrchestratorActionType)));

                        // TODO: Exception handling for invalid decisions, which is increasingly likely
                        //       when custom middleware is involved (e.g. out-of-process scenarios).
                        foreach (OrchestratorAction decision in decisions)
                        {
                            TraceHelper.TraceInstance(
                                TraceEventType.Information,
                                "TaskOrchestrationDispatcher-ProcessOrchestratorAction",
                                runtimeState.OrchestrationInstance!,
                                "Processing orchestrator action of type {0}",
                                decision.OrchestratorActionType);
                            switch (decision.OrchestratorActionType)
                            {
                                case OrchestratorActionType.ScheduleOrchestrator:
                                    var scheduleTaskAction = (ScheduleTaskOrchestratorAction)decision;
                                    var message = this.ProcessScheduleTaskDecision(
                                        scheduleTaskAction,
                                        runtimeState,
                                        this.IncludeParameters,
                                        traceActivity);
                                    messagesToSend.Add(message);
                                    break;
                                case OrchestratorActionType.CreateTimer:
                                    var timerOrchestratorAction = (CreateTimerOrchestratorAction)decision;
                                    timerMessages.Add(this.ProcessCreateTimerDecision(
                                        timerOrchestratorAction,
                                        runtimeState,
                                        isInternal: false));
                                    break;
                                case OrchestratorActionType.CreateSubOrchestration:
                                    var createSubOrchestrationAction = (CreateSubOrchestrationAction)decision;
                                    orchestratorMessages.Add(
                                        this.ProcessCreateSubOrchestrationInstanceDecision(
                                            createSubOrchestrationAction,
                                            runtimeState,
                                            this.IncludeParameters,
                                            traceActivity));
                                    break;
                                case OrchestratorActionType.SendEvent:
                                    var sendEventAction = (SendEventOrchestratorAction)decision;
                                    orchestratorMessages.Add(
                                        this.ProcessSendEventDecision(sendEventAction, runtimeState));
                                    break;
                                case OrchestratorActionType.OrchestrationComplete:
                                    OrchestrationCompleteOrchestratorAction completeDecision = (OrchestrationCompleteOrchestratorAction)decision;
                                    TaskMessage? workflowInstanceCompletedMessage =
                                        this.ProcessWorkflowCompletedTaskDecision(completeDecision, runtimeState, this.IncludeDetails, out continuedAsNew);
                                    if (workflowInstanceCompletedMessage != null)
                                    {
                                        // Send complete message to parent workflow or to itself to start a new execution
                                        // Store the event so we can rebuild the state
                                        carryOverEvents = null;
                                        if (continuedAsNew)
                                        {
                                            continuedAsNewMessage = workflowInstanceCompletedMessage;
                                            continueAsNewExecutionStarted = workflowInstanceCompletedMessage.Event as ExecutionStartedEvent;
                                            if (completeDecision.CarryoverEvents.Any())
                                            {
                                                carryOverEvents = completeDecision.CarryoverEvents.ToList();
                                                completeDecision.CarryoverEvents.Clear();
                                            }
                                        }
                                        else
                                        {
                                            orchestratorMessages.Add(workflowInstanceCompletedMessage);
                                        }
                                    }

                                    isCompleted = !continuedAsNew;
                                    break;
                                default:
                                    throw TraceHelper.TraceExceptionInstance(
                                        TraceEventType.Error,
                                        "TaskOrchestrationDispatcher-UnsupportedDecisionType",
                                        runtimeState.OrchestrationInstance!,
                                        new NotSupportedException($"Decision type '{decision.OrchestratorActionType}' not supported"));
                            }

                            // Underlying orchestration service provider may have a limit of messages per call, to avoid the situation
                            // we keep on asking the provider if message count is ok and stop processing new decisions if not.
                            //
                            // We also put in a fake timer to force next orchestration task for remaining messages
                            int totalMessages = messagesToSend.Count + orchestratorMessages.Count + timerMessages.Count;
                            if (this.orchestrationService.IsMaxMessageCountExceeded(totalMessages, runtimeState))
                            {
                                TraceHelper.TraceInstance(
                                    TraceEventType.Information,
                                    "TaskOrchestrationDispatcher-MaxMessageCountReached",
                                    runtimeState.OrchestrationInstance!,
                                    "MaxMessageCount reached.  Adding timer to process remaining events in next attempt.");

                                if (isCompleted || continuedAsNew)
                                {
                                    TraceHelper.TraceInstance(
                                        TraceEventType.Information,
                                        "TaskOrchestrationDispatcher-OrchestrationAlreadyCompleted",
                                        runtimeState.OrchestrationInstance!,
                                        "Orchestration already completed.  Skip adding timer for splitting messages.");
                                    break;
                                }

                                var dummyTimer = new CreateTimerOrchestratorAction
                                {
                                    Id = FrameworkConstants.FakeTimerIdToSplitDecision,
                                    FireAt = DateTime.UtcNow
                                };

                                timerMessages.Add(this.ProcessCreateTimerDecision(
                                    dummyTimer,
                                    runtimeState,
                                    isInternal: true));
                                isInterrupted = true;
                                break;
                            }
                        }

                        // correlation
                        CorrelationTraceClient.Propagate(() =>
                        {
                            if (runtimeState.ExecutionStartedEvent != null)
                                runtimeState.ExecutionStartedEvent.Correlation = CorrelationTraceContext.Current.SerializableTraceContext;
                        });


                        // finish up processing of the work item
                        if (!continuedAsNew && runtimeState.Events.Last().EventType != EventType.OrchestratorCompleted)
                        {
                            runtimeState.AddEvent(new OrchestratorCompletedEvent(-1));
                        }

                        if (isCompleted)
                        {
                            TraceHelper.TraceSession(TraceEventType.Information, "TaskOrchestrationDispatcher-DeletingSessionState", workItem.InstanceId, "Deleting session state");
                            if (runtimeState.ExecutionStartedEvent != null)
                            {
                                instanceState = Utils.BuildOrchestrationState(runtimeState);
                            }
                        }
                        else
                        {
                            if (continuedAsNew)
                            {
                                TraceHelper.TraceSession(
                                    TraceEventType.Information,
                                    "TaskOrchestrationDispatcher-UpdatingStateForContinuation",
                                    workItem.InstanceId,
                                    "Updating state for continuation");

                                // correlation
                                CorrelationTraceClient.Propagate(() =>
                                {
                                    continueAsNewExecutionStarted!.Correlation = CorrelationTraceContext.Current.SerializableTraceContext;
                                });

                                // Copy the distributed trace context, if any
                                continueAsNewExecutionStarted!.SetParentTraceContext(runtimeState.ExecutionStartedEvent);

                                runtimeState = new OrchestrationRuntimeState();
                                runtimeState.AddEvent(new OrchestratorStartedEvent(-1));
                                runtimeState.AddEvent(continueAsNewExecutionStarted!);
                                runtimeState.Status = workItem.OrchestrationRuntimeState.Status ?? carryOverStatus;
                                carryOverStatus = workItem.OrchestrationRuntimeState.Status;

                                if (carryOverEvents != null)
                                {
                                    foreach (var historyEvent in carryOverEvents)
                                    {
                                        runtimeState.AddEvent(historyEvent);
                                    }
                                }

                                runtimeState.AddEvent(new OrchestratorCompletedEvent(-1));
                                workItem.OrchestrationRuntimeState = runtimeState;

                                workItem.Cursor = null;
                            }

                            instanceState = Utils.BuildOrchestrationState(runtimeState);
                        }
                    } while (continuedAsNew);
                }
            }
            finally
            {
                if (renewTask != null)
                {
                    try
                    {

                        renewCancellationTokenSource.Cancel();
                        await renewTask;
                    }
                    catch (ObjectDisposedException)
                    {
                        // ignore
                    }
                    catch (OperationCanceledException)
                    {
                        // ignore
                    }
                }
            }

            if (workItem.RestoreOriginalRuntimeStateDuringCompletion)
            {
                // some backends expect the original runtime state object
                workItem.OrchestrationRuntimeState = originalOrchestrationRuntimeState;
            }

            runtimeState.Status = runtimeState.Status ?? carryOverStatus;

            if (instanceState != null)
            {
                instanceState.Status = runtimeState.Status;
            }

            await this.orchestrationService.CompleteTaskOrchestrationWorkItemAsync(
                workItem,
                runtimeState,
                continuedAsNew ? null : messagesToSend,
                orchestratorMessages,
                continuedAsNew ? null : timerMessages,
                continuedAsNewMessage,
                instanceState);
            
            if (workItem.RestoreOriginalRuntimeStateDuringCompletion)
            {
                workItem.OrchestrationRuntimeState = runtimeState;
            }

            return isCompleted || continuedAsNew || isInterrupted;
        }
```





## StopAsync() 方法

*Stops the dispatcher to stop getting and processing orchestration events*

停止调度程序，以停止获取和处理协调事件

```c#
        public async Task StopAsync(bool forced)
        {
            await this.dispatcher.StopAsync(forced);
        }
```





##  NonBlockingCountdownLock

一个 CountdownLock 的实现：

```c#
internal class NonBlockingCountdownLock
        {
            int available;

            public NonBlockingCountdownLock(int available)
            {
                if (available <= 0)
                {
                    throw new ArgumentOutOfRangeException(nameof(available));
                }

                this.available = available;
                this.Capacity = available;
            }

            public int Capacity { get; }

            public bool Acquire()
            {
                if (this.available <= 0)
                {
                    return false;
                }

                if (Interlocked.Decrement(ref this.available) >= 0)
                {
                    return true;
                }

                // the counter went negative - fix it
                Interlocked.Increment(ref this.available);
                return false;
            }

            public void Release()
            {
                Interlocked.Increment(ref this.available);
            }
        }
```




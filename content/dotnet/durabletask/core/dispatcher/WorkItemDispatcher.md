---
title: "WorkItemDispatcher源码学习"
linkTitle: "WorkItemDispatcher"
weight: 20
date: 2024-01-19
description: >
  学习durabletask WorkItemDispatcher的源码
---



Dispatcher class for fetching and processing work items of the supplied type

调度程序类，用于获取和处理所提供类型的工作项

`src\DurableTask.Core\WorkItemDispatcher.cs`



## 类定义



```c#
public class WorkItemDispatcher<T> : IDisposable
```



### 类字段



```c#
        const int DefaultMaxConcurrentWorkItems = 20;
        const int DefaultDispatcherCount = 1;

        const int BackOffIntervalOnInvalidOperationSecs = 10;
        const int CountDownToZeroDelay = 5;

        // ReSharper disable once StaticMemberInGenericType
        static readonly TimeSpan DefaultReceiveTimeout = TimeSpan.FromSeconds(30);
        readonly string id;
        readonly string name;
        readonly object thisLock = new object();
        readonly SemaphoreSlim initializationLock = new SemaphoreSlim(1, 1);

        volatile int concurrentWorkItemCount;
        volatile int countDownToZeroDelay;
        volatile int delayOverrideSecs;
        volatile int activeFetchers;
        bool isStarted;
        SemaphoreSlim concurrencyLock;
        CancellationTokenSource shutdownCancellationTokenSource;


```





### 方法定义



```c#
        readonly Func<T, string> workItemIdentifier;        

				Func<TimeSpan, CancellationToken, Task<T>> FetchWorkItem { get; }

        Func<T, Task> ProcessWorkItem { get; }
        
        /// <summary>
        /// Method to execute for safely releasing a work item
        /// </summary>
        public Func<T, Task> SafeReleaseWorkItem;

        /// <summary>
        /// Method to execute for aborting a work item
        /// </summary>
        public Func<T, Task> AbortWorkItem;

        /// <summary>
        /// Method to get a delay to wait after a fetch exception
        /// </summary>
        public Func<Exception, int> GetDelayInSecondsAfterOnFetchException = (exception) => 0;

        /// <summary>
        /// Method to get a delay to wait after a process exception
        /// </summary>
        public Func<Exception, int> GetDelayInSecondsAfterOnProcessException = (exception) => 0;
```





### 构造函数



```c#
        public WorkItemDispatcher(
            string name, 
            Func<T, string> workItemIdentifier,
            Func<TimeSpan, CancellationToken, Task<T>> fetchWorkItem,
            Func<T, Task> processWorkItem)
        {
            this.name = name;
            this.id = Guid.NewGuid().ToString("N");
            this.workItemIdentifier = workItemIdentifier ?? throw new ArgumentNullException(nameof(workItemIdentifier));
            this.FetchWorkItem = fetchWorkItem ?? throw new ArgumentNullException(nameof(fetchWorkItem));
            this.ProcessWorkItem = processWorkItem ?? throw new ArgumentNullException(nameof(processWorkItem));
        }
```

对照 TaskOrchestrationDispatcher 中的初始化代码：

```c#
internal TaskOrchestrationDispatcher(
            IOrchestrationService orchestrationService,
            INameVersionObjectManager<TaskOrchestration> objectManager,
            DispatchMiddlewarePipeline dispatchPipeline,
            LogHelper logHelper,
            ErrorPropagationMode errorPropagationMode)
        {
						......
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

						......
        }
```

Func<T, string> workItemIdentifier 的实现是:

```c#
item => item == null ? string.Empty : item.InstanceId,
```

fetchWorkItem 的实现是 TaskOrchestrationDispatcher.this.OnFetchWorkItemAsync，fetchWorkItem 的实现是 TaskOrchestrationDispatcher.this.OnProcessWorkItemSessionAsync，

DispatcherCount 由 orchestrationService.TaskOrchestrationDispatcherCount 指定，

MaxConcurrentWorkItems 由 orchestrationService.MaxConcurrentTaskOrchestrationWorkItems 指定。

## StartAsync()  方法

Starts the work item dispatcher：

```c#
public async Task StartAsync()
        {
            if (!this.isStarted)
            {
                await this.initializationLock.WaitAsync();
                try
                {
                    if (this.isStarted)
                    {
                        throw TraceHelper.TraceException(TraceEventType.Error, "WorkItemDispatcherStart-AlreadyStarted", new InvalidOperationException($"WorkItemDispatcher '{this.name}' has already started"));
                    }

                    this.concurrencyLock?.Dispose();
                    this.concurrencyLock = new SemaphoreSlim(this.MaxConcurrentWorkItems);

                    this.shutdownCancellationTokenSource?.Dispose();
                    this.shutdownCancellationTokenSource = new CancellationTokenSource();

                    this.isStarted = true;

                    TraceHelper.Trace(TraceEventType.Information, "WorkItemDispatcherStart", $"WorkItemDispatcher('{this.name}') starting. Id {this.id}.");

                    for (var i = 0; i < this.DispatcherCount; i++)
                    {
                        string dispatcherId = i.ToString();
                        // 创建 context
                        var context = new WorkItemDispatcherContext(this.name, this.id, dispatcherId);
                        this.LogHelper.DispatcherStarting(context);

                        // We just want this to Run we intentionally don't wait
                        // 我们只是想让它运行起来，我们故意不等待
                        #pragma warning disable 4014
                        Task.Run(() => this.DispatchAsync(context));
                        #pragma warning restore 4014
                    }
                }
                finally
                {
                    this.initializationLock.Release();
                }
            }
        }
```



### DispatchAsync() 方法



```c#
async Task DispatchAsync(WorkItemDispatcherContext context)
        {
            string dispatcherId = context.DispatcherId;

            bool logThrottle = true;
            while (this.isStarted)
            {
                if (!await this.concurrencyLock.WaitAsync(TimeSpan.FromSeconds(5)))
                {
                    if (logThrottle)
                    {
                        // This can happen frequently under heavy load.
                        // To avoid log spam, we log just once until we can proceed.
                        this.LogHelper.FetchingThrottled(
                            context,
                            this.concurrentWorkItemCount,
                            this.MaxConcurrentWorkItems);
                        TraceHelper.Trace(
                            TraceEventType.Warning,
                            "WorkItemDispatcherDispatch-MaxOperations",
                            this.GetFormattedLog(dispatcherId, $"Max concurrent operations ({this.concurrentWorkItemCount}) are already in progress. Still waiting for next accept."));
                        
                        logThrottle = false;
                    }

                    continue;
                }

                logThrottle = true;

                var delaySecs = 0;
                T workItem = default(T);
                try
                {
                    Interlocked.Increment(ref this.activeFetchers);
                    this.LogHelper.FetchWorkItemStarting(context, DefaultReceiveTimeout, this.concurrentWorkItemCount, this.MaxConcurrentWorkItems);
                    TraceHelper.Trace(
                        TraceEventType.Verbose, 
                        "WorkItemDispatcherDispatch-StartFetch",
                        this.GetFormattedLog(dispatcherId, $"Starting fetch with timeout of {DefaultReceiveTimeout} ({this.concurrentWorkItemCount}/{this.MaxConcurrentWorkItems} max)"));

                    Stopwatch timer = Stopwatch.StartNew();
                    // 在这里开始 fetch workitem
                    workItem = await this.FetchWorkItem(DefaultReceiveTimeout, this.shutdownCancellationTokenSource.Token);

                    if (!IsNull(workItem))
                    {
                        string workItemId = this.workItemIdentifier(workItem);
                        this.LogHelper.FetchWorkItemCompleted(
                            context,
                            workItemId,
                            timer.Elapsed,
                            this.concurrentWorkItemCount,
                            this.MaxConcurrentWorkItems);
                    }

                    TraceHelper.Trace(
                        TraceEventType.Verbose, 
                        "WorkItemDispatcherDispatch-EndFetch",
                        this.GetFormattedLog(dispatcherId, $"After fetch ({timer.ElapsedMilliseconds} ms) ({this.concurrentWorkItemCount}/{this.MaxConcurrentWorkItems} max)"));
                }
                catch (TimeoutException)
                {
                    delaySecs = 0;
                }
                catch (TaskCanceledException exception)
                {
                    TraceHelper.Trace(
                        TraceEventType.Information,
                        "WorkItemDispatcherDispatch-TaskCanceledException",
                        this.GetFormattedLog(dispatcherId, $"TaskCanceledException while fetching workItem, should be harmless: {exception.Message}"));
                    delaySecs = this.GetDelayInSecondsAfterOnFetchException(exception);
                }
                catch (Exception exception)
                {
                    if (!this.isStarted)
                    {
                        TraceHelper.Trace(
                            TraceEventType.Information, 
                            "WorkItemDispatcherDispatch-HarmlessException",
                            this.GetFormattedLog(dispatcherId, $"Harmless exception while fetching workItem after Stop(): {exception.Message}"));
                    }
                    else
                    {
                        this.LogHelper.FetchWorkItemFailure(context, exception);
                        // TODO : dump full node context here
                        TraceHelper.TraceException(
                            TraceEventType.Warning, 
                            "WorkItemDispatcherDispatch-Exception", 
                            exception,
                            this.GetFormattedLog(dispatcherId, $"Exception while fetching workItem: {exception.Message}"));
                        delaySecs = this.GetDelayInSecondsAfterOnFetchException(exception);
                    }
                }
                finally
                {
                    Interlocked.Decrement(ref this.activeFetchers);
                }

                // 然后开始调度 workitem
                var scheduledWorkItem = false;
                if (!IsNull(workItem))
                {
                    if (!this.isStarted)
                    {
                        if (this.SafeReleaseWorkItem != null)
                        {
                            await this.SafeReleaseWorkItem(workItem);
                        }
                    }
                    else
                    {
                        Interlocked.Increment(ref this.concurrentWorkItemCount);
                        // We just want this to Run we intentionally don't wait
                        #pragma warning disable 4014 
                       // 开始 workitem 的处理
                        Task.Run(() => this.ProcessWorkItemAsync(context, workItem));
                        #pragma warning restore 4014

                        scheduledWorkItem = true;
                    }
                }

                delaySecs = Math.Max(this.delayOverrideSecs, delaySecs);
                if (delaySecs > 0)
                {
                    await Task.Delay(TimeSpan.FromSeconds(delaySecs));
                }

                if (!scheduledWorkItem)
                {
                    this.concurrencyLock.Release();
                }
            }

            this.LogHelper.DispatcherStopped(context);
        }
```

这里的 FetchWorkItem() 方法和 ProcessWorkItemAsync() 方法都是由构造函数传递的（实际由 TaskOrchestrationDispatcher 传递）。









## StopAsync() 方法

*Stops the dispatcher to stop getting and processing orchestration events*

停止调度程序，以停止获取和处理协调事件

```c#
        public async Task StopAsync(bool forced)
        {
            await this.dispatcher.StopAsync(forced);
        }
```




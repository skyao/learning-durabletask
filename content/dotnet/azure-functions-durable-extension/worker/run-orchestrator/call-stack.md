---
title: "调用堆栈"
linkTitle: "调用堆栈"
weight: 100
date: 2024-01-19
description: >
  DurableTask client 运行 Orchestration 的调用堆栈
---



## 调用堆栈概况



```bash
MyDurableFunction1.dll!Company.Function.HelloOrchestration.RunOrchestrator(Microsoft.DurableTask.TaskOrchestrationContext context) Line 16 (c:\Users\sky\work\code\durabletask\MyDurableFunction1\HelloOrchestration.cs:16)
MyDurableFunction1.dll!MyDurableFunction1.DirectFunctionExecutor.ExecuteAsync(Microsoft.Azure.Functions.Worker.FunctionContext context) Line 32 (GeneratedFunctionExecutor.g.cs:32)
Microsoft.Azure.Functions.Worker.Core.dll!Microsoft.Azure.Functions.Worker.Pipeline.FunctionExecutionMiddleware.Invoke(Microsoft.Azure.Functions.Worker.FunctionContext context) Line 20 (FunctionExecutionMiddleware.cs:20)
Microsoft.Azure.Functions.Worker.Core.dll!Microsoft.Extensions.Hosting.MiddlewareWorkerApplicationBuilderExtensions.UseFunctionExecutionMiddleware.AnonymousMethod__1_2(Microsoft.Azure.Functions.Worker.FunctionContext context) Line 57 (WorkerMiddlewareWorkerApplicationBuilderExtensions.cs:57)
Microsoft.Azure.Functions.Worker.Core.dll!Microsoft.Azure.Functions.Worker.OutputBindings.OutputBindingsMiddleware.Invoke(Microsoft.Azure.Functions.Worker.FunctionContext context, Microsoft.Azure.Functions.Worker.Middleware.FunctionExecutionDelegate next) Line 13 (OutputBindingsMiddleware.cs:13)
Microsoft.Azure.Functions.Worker.Core.dll!Microsoft.Extensions.Hosting.MiddlewareWorkerApplicationBuilderExtensions.UseOutputBindingsMiddleware.AnonymousMethod__3(Microsoft.Azure.Functions.Worker.FunctionContext context) Line 84 (WorkerMiddlewareWorkerApplicationBuilderExtensions.cs:84)
Microsoft.Azure.Functions.Worker.Extensions.Http.AspNetCore.dll!Microsoft.Azure.Functions.Worker.Extensions.Http.AspNetCore.FunctionsHttpProxyingMiddleware.Invoke(Microsoft.Azure.Functions.Worker.FunctionContext context, Microsoft.Azure.Functions.Worker.Middleware.FunctionExecutionDelegate next) Line 34 (FunctionsHttpProxyingMiddleware.cs:34)
Microsoft.Azure.Functions.Worker.Core.dll!Microsoft.Extensions.Hosting.MiddlewareWorkerApplicationBuilderExtensions.UseMiddleware.AnonymousMethod__1(Microsoft.Azure.Functions.Worker.FunctionContext context) Line 105 (WorkerMiddlewareWorkerApplicationBuilderExtensions.cs:105)
Microsoft.Azure.Functions.Worker.Extensions.DurableTask.dll!Microsoft.Azure.Functions.Worker.Extensions.DurableTask.FunctionsOrchestrator.EnsureSynchronousExecution(Microsoft.Azure.Functions.Worker.FunctionContext functionContext, Microsoft.Azure.Functions.Worker.Middleware.FunctionExecutionDelegate next, Microsoft.Azure.Functions.Worker.Extensions.DurableTask.FunctionsOrchestrationContext orchestrationContext) Line 72 (c:\Users\sky\work\code\durabletask-fork\azure-functions-durable-extension\src\Worker.Extensions.DurableTask\FunctionsOrchestrator.cs:72)
Microsoft.Azure.Functions.Worker.Extensions.DurableTask.dll!Microsoft.Azure.Functions.Worker.Extensions.DurableTask.FunctionsOrchestrator.RunAsync(Microsoft.DurableTask.TaskOrchestrationContext context, object input) Line 51 (c:\Users\sky\work\code\durabletask-fork\azure-functions-durable-extension\src\Worker.Extensions.DurableTask\FunctionsOrchestrator.cs:51)
Microsoft.DurableTask.Worker.dll!Microsoft.DurableTask.Worker.Shims.TaskOrchestrationShim.Execute(DurableTask.Core.OrchestrationContext innerContext, string rawInput) Line 52 (c:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\src\Worker\Core\Shims\TaskOrchestrationShim.cs:52)
DurableTask.Core.dll!DurableTask.Core.TaskOrchestrationExecutor.ProcessEvent(DurableTask.Core.History.HistoryEvent historyEvent) Line 211 (TaskOrchestrationExecutor.cs:211)
DurableTask.Core.dll!DurableTask.Core.TaskOrchestrationExecutor.ExecuteCore.__ProcessEvents|12_0(System.Collections.Generic.IEnumerable<DurableTask.Core.History.HistoryEvent> events) Line 135 (TaskOrchestrationExecutor.cs:135)
DurableTask.Core.dll!DurableTask.Core.TaskOrchestrationExecutor.ExecuteCore(System.Collections.Generic.IEnumerable<DurableTask.Core.History.HistoryEvent> pastEvents, System.Collections.Generic.IEnumerable<DurableTask.Core.History.HistoryEvent> newEvents) Line 143 (TaskOrchestrationExecutor.cs:143)
DurableTask.Core.dll!DurableTask.Core.TaskOrchestrationExecutor.Execute() Line 93 (TaskOrchestrationExecutor.cs:93)
Microsoft.DurableTask.Worker.Grpc.dll!Microsoft.DurableTask.Worker.Grpc.GrpcOrchestrationRunner.LoadAndRun(string encodedOrchestratorRequest, Microsoft.DurableTask.ITaskOrchestrator implementation, System.IServiceProvider services) Line 113 (c:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\src\Worker\Grpc\GrpcOrchestrationRunner.cs:113)
Microsoft.Azure.Functions.Worker.Extensions.DurableTask.dll!Microsoft.Azure.Functions.Worker.Extensions.DurableTask.DurableTaskFunctionsMiddleware.RunOrchestrationAsync(Microsoft.Azure.Functions.Worker.FunctionContext context, Microsoft.Azure.Functions.Worker.BindingMetadata triggerBinding, Microsoft.Azure.Functions.Worker.Middleware.FunctionExecutionDelegate next) Line 59 (c:\Users\sky\work\code\durabletask-fork\azure-functions-durable-extension\src\Worker.Extensions.DurableTask\DurableTaskFunctionsMiddleware.cs:59)
Microsoft.Azure.Functions.Worker.Extensions.DurableTask.dll!Microsoft.Azure.Functions.Worker.Extensions.DurableTask.DurableTaskFunctionsMiddleware.Invoke(Microsoft.Azure.Functions.Worker.FunctionContext functionContext, Microsoft.Azure.Functions.Worker.Middleware.FunctionExecutionDelegate next) Line 22 (c:\Users\sky\work\code\durabletask-fork\azure-functions-durable-extension\src\Worker.Extensions.DurableTask\DurableTaskFunctionsMiddleware.cs:22)
Microsoft.Azure.Functions.Worker.Core.dll!Microsoft.Extensions.Hosting.MiddlewareWorkerApplicationBuilderExtensions.UseMiddleware.AnonymousMethod__1(Microsoft.Azure.Functions.Worker.FunctionContext context) Line 105 (WorkerMiddlewareWorkerApplicationBuilderExtensions.cs:105)
Microsoft.Azure.Functions.Worker.Core.dll!Microsoft.Azure.Functions.Worker.FunctionsApplication.InvokeFunctionAsync(Microsoft.Azure.Functions.Worker.FunctionContext context) Line 77 (FunctionsApplication.cs:77)
Microsoft.Azure.Functions.Worker.Grpc.dll!Microsoft.Azure.Functions.Worker.Handlers.InvocationHandler.InvokeAsync(Microsoft.Azure.Functions.Worker.Grpc.Messages.InvocationRequest request) Line 88 (InvocationHandler.cs:88)
Microsoft.Azure.Functions.Worker.Grpc.dll!Microsoft.Azure.Functions.Worker.GrpcWorker.InvocationRequestHandlerAsync(Microsoft.Azure.Functions.Worker.Grpc.Messages.InvocationRequest request) Line 122 (GrpcWorker.cs:122)
Microsoft.Azure.Functions.Worker.Grpc.dll!Microsoft.Azure.Functions.Worker.GrpcWorker.ProcessRequestCoreAsync(Microsoft.Azure.Functions.Worker.Grpc.Messages.StreamingMessage request) Line 81 (GrpcWorker.cs:81)
Microsoft.Azure.Functions.Worker.Grpc.dll!Microsoft.Azure.Functions.Worker.GrpcWorker.Microsoft.Azure.Functions.Worker.Grpc.IMessageProcessor.ProcessMessageAsync.AnonymousMethod__0() Line 66 (GrpcWorker.cs:66)
System.Private.CoreLib.dll!System.Threading.Tasks.Task<System.Threading.Tasks.Task>.InnerInvoke() (Unknown Source:0)
System.Private.CoreLib.dll!System.Threading.ExecutionContext.RunFromThreadPoolDispatchLoop(System.Threading.Thread threadPoolThread, System.Threading.ExecutionContext executionContext, System.Threading.ContextCallback callback, object state) (Unknown Source:0)
System.Private.CoreLib.dll!System.Threading.Tasks.Task.ExecuteWithThreadLocal(ref System.Threading.Tasks.Task currentTaskSlot, System.Threading.Thread threadPoolThread) (Unknown Source:0)
System.Private.CoreLib.dll!System.Threading.ThreadPoolWorkQueue.Dispatch() (Unknown Source:0)
System.Private.CoreLib.dll!System.Threading.PortableThreadPool.WorkerThread.WorkerThreadStart() (Unknown Source:0)
[Native to Managed Transition] (Unknown Source:0)
```





## Azure function dotnet worker



### GrpcWorkerClientFactory

```c#
            private async Task StartReaderAsync(IAsyncStreamReader<StreamingMessage> responseStream)
            {
                while (await responseStream.MoveNext())
                {
                    await _processor!.ProcessMessageAsync(responseStream.Current);
                }
            }
```

这里的 _processor 实现的 `Microsoft.Azure.Functions.Worker.GrpcWorker`

### GrpcWorker

grpc worker 收到 grpc 消息之后，调用 ProcessRequestCoreAsync() 方法进行处理，注意这里是异步：

```c#
        Task IMessageProcessor.ProcessMessageAsync(StreamingMessage message)
        {
            // Dispatch and return.
            Task.Run(() => ProcessRequestCoreAsync(message));

            return Task.CompletedTask;
        }
```

ProcessRequestCoreAsync() 方法的实现：

```c#
        private async Task ProcessRequestCoreAsync(StreamingMessage request)
        {
            StreamingMessage responseMessage = new StreamingMessage
            {
                RequestId = request.RequestId
            };

            switch (request.ContentCase)
            {
                case MsgType.InvocationRequest:
                    // 会走到这里
                    responseMessage.InvocationResponse = await InvocationRequestHandlerAsync(request.InvocationRequest);
                    break;

                case MsgType.WorkerInitRequest:
                    responseMessage.WorkerInitResponse = WorkerInitRequestHandler(request.WorkerInitRequest, _workerOptions);
                    break;

                case MsgType.WorkerStatusRequest:
                    responseMessage.WorkerStatusResponse = new WorkerStatusResponse();
                    break;

                case MsgType.FunctionsMetadataRequest:
                    responseMessage.FunctionMetadataResponse = await GetFunctionMetadataAsync(request.FunctionsMetadataRequest.FunctionAppDirectory);
                    break;

                case MsgType.WorkerTerminate:
                    WorkerTerminateRequestHandler(request.WorkerTerminate);
                    break;

                case MsgType.FunctionLoadRequest:
                    responseMessage.FunctionLoadResponse = FunctionLoadRequestHandler(request.FunctionLoadRequest, _application, _methodInfoLocator);
                    break;

                case MsgType.FunctionEnvironmentReloadRequest:
                    responseMessage.FunctionEnvironmentReloadResponse = EnvironmentReloadRequestHandler(_workerOptions);
                    break;

                case MsgType.InvocationCancel:
                    InvocationCancelRequestHandler(request.InvocationCancel);
                    break;

                default:
                    // TODO: Trace failure here.
                    return;
            }

            await _workerClient!.SendMessageAsync(responseMessage);
        }
```

InvocationRequestHandlerAsync() 方法

```c#
        internal Task<InvocationResponse> InvocationRequestHandlerAsync(InvocationRequest request)
        {
            return _invocationHandler.InvokeAsync(request);
        }
```

这里的 _invocationHandler 的定义类型是 IInvocationHandler，实现是 `Microsoft.Azure.Functions.Worker.Handlers.InvocationHandler`

### InvocationHandler

 代码在 `azure-functions-dotnet-worker` 仓库下的 `src\DotNetWorker.Grpc\Handlers\InvocationHandler.cs` :

```c#
        public async Task<InvocationResponse> InvokeAsync(InvocationRequest request)
        {
            using CancellationTokenSource cancellationTokenSource = new();
            FunctionContext? context = null;
            InvocationResponse response = new()
            {
                InvocationId = request.InvocationId,
                Result = new StatusResult()
            };

            if (!_inflightInvocations.TryAdd(request.InvocationId, cancellationTokenSource))
            {
                var exception = new InvalidOperationException("Unable to track CancellationTokenSource");
                response.Result.Status = StatusResult.Types.Status.Failure;
                response.Result.Exception = exception.ToRpcException();

                return response;
            }

            try
            {
                var invocation = new GrpcFunctionInvocation(request);

                IInvocationFeatures invocationFeatures = _invocationFeaturesFactory.Create();
                invocationFeatures.Set<FunctionInvocation>(invocation);
                invocationFeatures.Set<IExecutionRetryFeature>(invocation);

                context = _application.CreateContext(invocationFeatures, cancellationTokenSource.Token);
                invocationFeatures.Set<IFunctionBindingsFeature>(new GrpcFunctionBindingsFeature(context, request, _outputBindingsInfoProvider));

                if (_inputConversionFeatureProvider.TryCreate(typeof(DefaultInputConversionFeature), out var conversion))
                {
                    invocationFeatures.Set<IInputConversionFeature>(conversion!);
                }

                // 进入这里
                await _application.InvokeFunctionAsync(context);

                var serializer = _workerOptions.Serializer!;
                var functionBindings = context.GetBindings();

                foreach (var binding in functionBindings.OutputBindingData)
                {
                    var parameterBinding = new ParameterBinding
                    {
                        Name = binding.Key
                    };

                    if (binding.Value is not null)
                    {
                        parameterBinding.Data = await binding.Value.ToRpcAsync(serializer);
                    }

                    response.OutputData.Add(parameterBinding);
                }

                if (functionBindings.InvocationResult is not null)
                {
                    TypedData? returnVal = await functionBindings.InvocationResult.ToRpcAsync(serializer);
                    response.ReturnValue = returnVal;
                }

                response.Result.Status = StatusResult.Types.Status.Success;
            }
            catch (Exception ex)
            {
                response.Result.Exception = _workerOptions.EnableUserCodeException ? ex.ToUserRpcException() : ex.ToRpcException();
                response.Result.Status = StatusResult.Types.Status.Failure;

                if (ex.InnerException is TaskCanceledException or OperationCanceledException)
                {
                    response.Result.Status = StatusResult.Types.Status.Cancelled;
                }
            }
            finally
            {
                _inflightInvocations.TryRemove(request.InvocationId, out var cts);

                if (context is IAsyncDisposable asyncContext)
                {
                    await asyncContext.DisposeAsync();
                }

                (context as IDisposable)?.Dispose();
            }

            return response;
        }
```

_application 的定义类型是IFunctionsApplication ，实际实现是 `Microsoft.Azure.Functions.Worker.FunctionsApplication`

### FunctionsApplication

` 代码在 `azure-functions-dotnet-worker` 仓库下的 src\DotNetWorker.Core\FunctionsApplication.cs`

```c#
		public async Task InvokeFunctionAsync(FunctionContext context)
        {
            var scope = new FunctionInvocationScope(context.FunctionDefinition.Name, context.InvocationId);

            using var logScope = _logger.BeginScope(scope);
            using Activity? invokeActivity = _functionActivitySourceFactory.StartInvoke(context);

            try
            {
                // 进入这里
                await _functionExecutionDelegate(context);
            }
            catch (Exception ex)
            {
                invokeActivity?.SetStatus(ActivityStatusCode.Error, ex.Message);

                Log.InvocationError(_logger, context.FunctionDefinition.Name, context.InvocationId, ex);

                throw;
            }
        }
```

_functionExecutionDelegate  的实现是 `Microsoft.Azure.Functions.Worker.Middleware.FunctionExecutionDelegate`



### MiddlewareWorkerApplicationBuilderExtensions

```c#
        public static IFunctionsWorkerApplicationBuilder UseMiddleware<T>(this IFunctionsWorkerApplicationBuilder builder)
            where T : class, IFunctionsWorkerMiddleware
        {
            builder.Services.AddSingleton<T>();

            builder.Use(next =>
            {
                return context =>
                {
                    var middleware = context.InstanceServices.GetRequiredService<T>();

                    return middleware.Invoke(context, next);
                };
            });

            return builder;
        }
```

这里的 middleware 实现是 DurableTaskFunctionsMiddleware

## Azure functions durable extension

### DurableTaskFunctionsMiddleware



```c#
    public Task Invoke(FunctionContext functionContext, FunctionExecutionDelegate next)
    {
        if (IsOrchestrationTrigger(functionContext, out BindingMetadata? triggerBinding))
        {
            // 代码进入这里
            return RunOrchestrationAsync(functionContext, triggerBinding, next);
        }

        if (IsEntityTrigger(functionContext, out triggerBinding))
        {
            return RunEntityAsync(functionContext, triggerBinding, next);
        }

        return next(functionContext);
    }
```

RunOrchestrationAsync() 方法的实现：



```c#
    static async Task RunOrchestrationAsync(
        FunctionContext context, BindingMetadata triggerBinding, FunctionExecutionDelegate next)
    {
        InputBindingData<object> triggerInputData = await context.BindInputAsync<object>(triggerBinding);
        if (triggerInputData?.Value is not string encodedOrchestratorState)
        {
            throw new InvalidOperationException("Orchestration history state was either missing from the input or not a string value.");
        }

        FunctionsOrchestrator orchestrator = new(context, next, triggerInputData);
        string orchestratorOutput = GrpcOrchestrationRunner.LoadAndRun(
            encodedOrchestratorState, orchestrator, context.InstanceServices);

        // Send the encoded orchestrator output as the return value seen by the functions host extension
        context.GetInvocationResult().Value = orchestratorOutput;
    }
```





### GrpcOrchestrationRunner

`durabletask-dotnet`  仓库下的 `\src\Worker\Grpc\GrpcOrchestrationRunner.cs`

```c#
    public static string LoadAndRun(
        string encodedOrchestratorRequest,
        ITaskOrchestrator implementation,
        IServiceProvider? services = null)
    {
        Check.NotNullOrEmpty(encodedOrchestratorRequest);
        Check.NotNull(implementation);

        P.OrchestratorRequest request = P.OrchestratorRequest.Parser.Base64Decode<P.OrchestratorRequest>(
            encodedOrchestratorRequest);

        List<HistoryEvent> pastEvents = request.PastEvents.Select(ProtoUtils.ConvertHistoryEvent).ToList();
        IEnumerable<HistoryEvent> newEvents = request.NewEvents.Select(ProtoUtils.ConvertHistoryEvent);

        // Re-construct the orchestration state from the history.
        // New events must be added using the AddEvent method.
        OrchestrationRuntimeState runtimeState = new(pastEvents);
        foreach (HistoryEvent newEvent in newEvents)
        {
            runtimeState.AddEvent(newEvent);
        }

        TaskName orchestratorName = new(runtimeState.Name);
        ParentOrchestrationInstance? parent = runtimeState.ParentInstance is ParentInstance p
            ? new(new(p.Name), p.OrchestrationInstance.InstanceId)
            : null;

        DurableTaskShimFactory factory = services is null
            ? DurableTaskShimFactory.Default
            : ActivatorUtilities.GetServiceOrCreateInstance<DurableTaskShimFactory>(services);
        TaskOrchestration shim = factory.CreateOrchestration(orchestratorName, implementation, parent);
        TaskOrchestrationExecutor executor = new(runtimeState, shim, BehaviorOnContinueAsNew.Carryover, request.EntityParameters.ToCore(), ErrorPropagationMode.UseFailureDetails);
        // 代码进入这里
        OrchestratorExecutionResult result = executor.Execute();

        P.OrchestratorResponse response = ProtoUtils.ConstructOrchestratorResponse(
            request.InstanceId,
            result.CustomStatus,
            result.Actions);
        byte[] responseBytes = response.ToByteArray();
        return Convert.ToBase64String(responseBytes);
    }
```

### TaskOrchestrationExecutor

`DurableTask.Core.TaskOrchestrationExecutor` 这个类在 `Azure/durabletask` 项目中

```c#
        public OrchestratorExecutionResult Execute()
        {
            return this.ExecuteCore(
                pastEvents: this.orchestrationRuntimeState.PastEvents,
                newEvents: this.orchestrationRuntimeState.NewEvents);
        }
```

第一次执行时，PastEvents 为空，NewEvents 里面有两个 event：

- DurableTask.Core.History.OrchestratorStartedEvent
- DurableTask.Core.History.ExecutionStartedEvent

ExecuteCore() 方法的实现：

```c#
       OrchestratorExecutionResult ExecuteCore(IEnumerable<HistoryEvent> pastEvents, IEnumerable<HistoryEvent> newEvents)
        {
            SynchronizationContext prevCtx = SynchronizationContext.Current;

            try
            {
                SynchronizationContext syncCtx = new TaskOrchestrationSynchronizationContext(this.decisionScheduler);
                SynchronizationContext.SetSynchronizationContext(syncCtx);
                OrchestrationContext.IsOrchestratorThread = true;

                try
                {
                    void ProcessEvents(IEnumerable<HistoryEvent> events)
                    {
                        foreach (HistoryEvent historyEvent in events)
                        {
                            if (historyEvent.EventType == EventType.OrchestratorStarted)
                            {
                                var decisionStartedEvent = (OrchestratorStartedEvent)historyEvent;
                                this.context.CurrentUtcDateTime = decisionStartedEvent.Timestamp;
                                continue;
                            }

                            // 进入这里
                            this.ProcessEvent(historyEvent);
                            historyEvent.IsPlayed = true;
                        }
                    }

                    // Replay the old history to rebuild the local state of the orchestration.
                    // TODO: Log a verbose message indicating that the replay has started (include event count?)
                    this.context.IsReplaying = true;
                    ProcessEvents(pastEvents);

                    // Play the newly arrived events to determine the next action to take.
                    // TODO: Log a verbose message indicating that new events are being processed (include event count?)
                    this.context.IsReplaying = false;
                    // 第一次调用会进去这里，IsReplaying 设置为 false
                    ProcessEvents(newEvents);

                    // check if workflow is completed after this replay
                    // TODO: Create a setting that allows orchestrations to complete when the orchestrator
                    //       function completes, even if there are open tasks.
                    if (!this.context.HasOpenTasks)
                    {
                        if (this.result!.IsCompleted)
                        {
                            if (this.result.IsFaulted)
                            {
                                Exception? exception = this.result.Exception?.InnerExceptions.FirstOrDefault();
                                Debug.Assert(exception != null);

                                if (Utils.IsExecutionAborting(exception!))
                                {
                                    // Let this exception propagate out to be handled by the dispatcher
                                    ExceptionDispatchInfo.Capture(exception).Throw();
                                }
                                
                                this.context.FailOrchestration(exception);
                            }
                            else
                            {
                                this.context.CompleteOrchestration(this.result.Result);
                            }
                        }

                        // TODO: It is an error if result is not completed when all OpenTasks are done.
                        // Throw an exception in that case.
                    }
                }
                catch (NonDeterministicOrchestrationException exception)
                {
                    this.context.FailOrchestration(exception);
                }

                return new OrchestratorExecutionResult
                {
                    Actions = this.context.OrchestratorActions,
                    CustomStatus = this.taskOrchestration.GetStatus(),
                };
            }
            finally
            {
                SynchronizationContext.SetSynchronizationContext(prevCtx);
                OrchestrationContext.IsOrchestratorThread = false;
            }
        }
```

ProcessEvent() 方法

```c#
void ProcessEvent(HistoryEvent historyEvent)
        {
            bool overrideSuspension = historyEvent.EventType == EventType.ExecutionResumed || historyEvent.EventType == EventType.ExecutionTerminated;
            if (this.context.IsSuspended && !overrideSuspension)
            {
                this.context.HandleEventWhileSuspended(historyEvent);
            }
            else
            {
                switch (historyEvent.EventType)
                {
                    case EventType.ExecutionStarted:
                        // 执行这里的代码
                        var executionStartedEvent = (ExecutionStartedEvent)historyEvent;
                        this.result = this.taskOrchestration.Execute(this.context, executionStartedEvent.Input);
                        break;
                    case EventType.ExecutionTerminated:
                        this.context.HandleExecutionTerminatedEvent((ExecutionTerminatedEvent)historyEvent);
                        break;
                    case EventType.TaskScheduled:
                        this.context.HandleTaskScheduledEvent((TaskScheduledEvent)historyEvent);
                        break;
                    case EventType.TaskCompleted:
                        this.context.HandleTaskCompletedEvent((TaskCompletedEvent)historyEvent);
                        break;
                    case EventType.TaskFailed:
                        this.context.HandleTaskFailedEvent((TaskFailedEvent)historyEvent);
                        break;
                    case EventType.SubOrchestrationInstanceCreated:
                        this.context.HandleSubOrchestrationCreatedEvent((SubOrchestrationInstanceCreatedEvent)historyEvent);
                        break;
                    case EventType.SubOrchestrationInstanceCompleted:
                        this.context.HandleSubOrchestrationInstanceCompletedEvent(
                            (SubOrchestrationInstanceCompletedEvent)historyEvent);
                        break;
                    case EventType.SubOrchestrationInstanceFailed:
                        this.context.HandleSubOrchestrationInstanceFailedEvent((SubOrchestrationInstanceFailedEvent)historyEvent);
                        break;
                    case EventType.TimerCreated:
                        this.context.HandleTimerCreatedEvent((TimerCreatedEvent)historyEvent);
                        break;
                    case EventType.TimerFired:
                        this.context.HandleTimerFiredEvent((TimerFiredEvent)historyEvent);
                        break;
                    case EventType.EventSent:
                        this.context.HandleEventSentEvent((EventSentEvent)historyEvent);
                        break;
                    case EventType.EventRaised:
                        this.context.HandleEventRaisedEvent((EventRaisedEvent)historyEvent, this.skipCarryOverEvents, this.taskOrchestration);
                        break;
                    case EventType.ExecutionSuspended:
                        this.context.HandleExecutionSuspendedEvent((ExecutionSuspendedEvent)historyEvent);
                        break;
                    case EventType.ExecutionResumed:
                        this.context.HandleExecutionResumedEvent((ExecutionResumedEvent)historyEvent, ProcessEvent);
                        break;
                }
            }
        }
```

> versioning TODO: 这里的 ExecutionStartedEvent 的 version 字段暂时为空，后面需要更新。



### TaskOrchestrationShim

```c#
    public override async Task<string?> Execute(OrchestrationContext innerContext, string rawInput)
    {
        Check.NotNull(innerContext);
        JsonDataConverterShim converterShim = new(this.invocationContext.Options.DataConverter);
        innerContext.MessageDataConverter = converterShim;
        innerContext.ErrorDataConverter = converterShim;

        object? input = this.DataConverter.Deserialize(rawInput, this.implementation.InputType);
        this.wrapperContext = new(innerContext, this.invocationContext, input);

        try
        {
            object? output = await this.implementation.RunAsync(this.wrapperContext, input);

            // Return the output (if any) as a serialized string.
            return this.DataConverter.Serialize(output);
        }
        finally
        {
            // if user code crashed inside a critical section, or did not exit it, do that now
            this.wrapperContext.ExitCriticalSectionIfNeeded();
        }
    }
```

> versioning TODO: 这里的 OrchestrationContext 的 OrchestrationInstance 字段只包含 InstanceId 和 ExecutionId，需要增加一个 InstanceVersion 字段，其值应该从 ExecutionStartedEvent 的 version 字段中获取。

wrapperContext 的实现是 TaskOrchestrationContextWrapper 

> versioning TODO:  TaskOrchestrationContextWrapper 需要增加一个 InstanceVersion 字段，其值从 this.innerContext.OrchestrationInstance.InstanceVersion 中获取



 this.implementation 的实现是 `Microsoft.Azure.Functions.Worker.Extensions.DurableTask.FunctionsOrchestrator`

### FunctionsOrchestrator





```c#
public async Task<object?> RunAsync(TaskOrchestrationContext context, object? input)
    {
        // Set the function input to be the orchestration context wrapped in our own object so that we can
        // intercept any of the calls and inject our own logic or tracking.
        FunctionsOrchestrationContext wrapperContext = new(context, this.functionContext);
        this.contextBinding.Value = wrapperContext;
        this.inputContext.PrepareInput(input);

        try
        {
            // This method will advance to the next middleware and throw if it detects an asynchronous execution.
            await EnsureSynchronousExecution(this.functionContext, this.next, wrapperContext);
        }
        catch (Exception ex)
        {
            this.functionContext.GetLogger<FunctionsOrchestrator>().LogError(
                ex,
                "An error occurred while executing the orchestrator function '{FunctionName}'.",
                this.functionContext.FunctionDefinition.Name);
            throw;
        }

        // Set the raw function output as the orchestrator output
        object? functionOutput = this.functionContext.GetInvocationResult().Value;
        return functionOutput;
    }
```



EnsureSynchronousExecution的实现：

```c#
    private static async Task EnsureSynchronousExecution(
        FunctionContext functionContext,
        FunctionExecutionDelegate next,
        FunctionsOrchestrationContext orchestrationContext)
    {
        Task orchestratorTask = next(functionContext);
        if (!orchestratorTask.IsCompleted && !orchestrationContext.IsAccessed)
        {
            // If the middleware returns before the orchestrator function's context object was accessed and before
            // it completes its execution, then we know that either some middleware component went async or that the
            // orchestrator function did some illegal await as its very first action.
            throw new InvalidOperationException(Constants.IllegalAwaitErrorMessage);
        }

        await orchestratorTask;

        // This will throw if either the orchestrator performed an illegal await or if some middleware ahead of this
        // one performed some illegal await.
        orchestrationContext.ThrowIfIllegalAccess();
    }
```

next 函数的实现是在前面定义的：

```c#
                return context =>
                {
                    var middleware = context.InstanceServices.GetRequiredService<T>();

                    return middleware.Invoke(context, next);
                };
```

## Azure-functions-dotnet-worker

### FunctionExecutionMiddleware



```c#
public async Task Invoke(FunctionContext context, FunctionExecutionDelegate next)
        {
            // Only use the coordinator for HttpTriggers
            if (!_isHttpTrigger.GetOrAdd(context.FunctionId, static (_, c) => IsHttpTriggerFunction(c), context))
            {
                await next(context);
                return;
            }

            var invocationId = context.InvocationId;

            // this call will block until the ASP.NET middleware pipeline has signaled that it's ready to run the function
            var httpContext = await _coordinator.SetFunctionContextAsync(invocationId, context);

            AddHttpContextToFunctionContext(context, httpContext);

            // Register additional context features
            context.Features.Set<IFromBodyConversionFeature>(FromBodyConverstionFeature.Instance);

            await next(context);

            var invocationResult = context.GetInvocationResult();

            if (invocationResult?.Value is IActionResult actionResult)
            {
                ActionContext actionContext = new ActionContext(httpContext, httpContext.GetRouteData(), new ActionDescriptor());

                await actionResult.ExecuteResultAsync(actionContext);
            }
            else if (invocationResult?.Value is AspNetCoreHttpResponseData)
            {
                // The AspNetCoreHttpResponseData implementation is
                // simply a wrapper over the underlying HttpResponse and
                // all APIs manipulate the request.
                // There's no need to return this result as no additional
                // processing is required.
                invocationResult.Value = null;
            }

            // allows asp.net middleware to continue
            _coordinator.CompleteFunctionInvocation(invocationId);
        }
```



```c#
        public Task Invoke(FunctionContext context)
        {
            return _functionExecutor.ExecuteAsync(context).AsTask();
        }
```



这里的 _functionExecutor 的实现是 MyDurableFunction1.DirectFunctionExecutor

### OutputBindingsMiddleware 

```c#
        public static async Task Invoke(FunctionContext context, FunctionExecutionDelegate next)
        {
            await next(context);

            AddOutputBindings(context);
        }
```





### Work.Sdk.Generator.GeneratedFunctionExecutor

被 GeneratedFunctionExecutor.g.cs 调用：

```c#
        public async ValueTask ExecuteAsync(FunctionContext context)
        {
            var inputBindingFeature = context.Features.Get<IFunctionInputBindingFeature>();
            var inputBindingResult = await inputBindingFeature.BindFunctionInputAsync(context);
            var inputArguments = inputBindingResult.Values;

            if (string.Equals(context.FunctionDefinition.EntryPoint, "Company.Function.HelloOrchestration.RunOrchestrator", StringComparison.Ordinal))
            {
                context.GetInvocationResult().Value = await global::Company.Function.HelloOrchestration.RunOrchestrator((global::Microsoft.DurableTask.TaskOrchestrationContext)inputArguments[0]);
            }
            else if (string.Equals(context.FunctionDefinition.EntryPoint, "Company.Function.HelloOrchestration.SayHello", StringComparison.Ordinal))
            {
                context.GetInvocationResult().Value = global::Company.Function.HelloOrchestration.SayHello((string)inputArguments[0], (global::Microsoft.Azure.Functions.Worker.FunctionContext)inputArguments[1]);
            }
            else if (string.Equals(context.FunctionDefinition.EntryPoint, "Company.Function.HelloOrchestration.HttpStart", StringComparison.Ordinal))
            {
                context.GetInvocationResult().Value = await global::Company.Function.HelloOrchestration.HttpStart((global::Microsoft.Azure.Functions.Worker.Http.HttpRequestData)inputArguments[0], (global::Microsoft.DurableTask.Client.DurableTaskClient)inputArguments[1], (global::Microsoft.Azure.Functions.Worker.FunctionContext)inputArguments[2]);
            }
        }
```

通过检查 `context.FunctionDefinition.EntryPoint` 的值，如果为以下值时，则分别调用对应的 function：

| `context.FunctionDefinition.EntryPoint` 的值          | function                             | funciton source code                       |
| ----------------------------------------------------- | ------------------------------------ | ------------------------------------------ |
| "Company.Function.HelloOrchestration.SayHello"        | HelloOrchestration.RunOrchestrator() | [Function(nameof(HelloOrchestration))]     |
| "Company.Function.HelloOrchestration.RunOrchestrator" | HelloOrchestration.SayHello()        | [Function(nameof(SayHello))]               |
| "Company.Function.HelloOrchestration.HttpStart"       | HelloOrchestration.HttpStart()       | [Function("HelloOrchestration_HttpStart")] |

其中，HttpStart() function 是用来接受 http 请求然后出发 Schedule New Orchestration Instance 操作的。

之后 Orchestration Engine 就会启动 Orchestration ，然后 RunOrchestrator() 方法被执行。

```c#
context.GetInvocationResult().Value = await global::Company.Function.HelloOrchestration.RunOrchestrator((global::Microsoft.DurableTask.TaskOrchestrationContext)inputArguments[0]);
```

## Customer Code

### HelloOrchestration function

以 quickstart `HelloOrchestration.cs` 为例：

```c#
        [Function(nameof(HelloOrchestration))]
        public static async Task<List<string>> RunOrchestrator(
            [OrchestrationTrigger("1.5.6")] TaskOrchestrationContext context)
        {
           var instanceId = context.InstanceId;
           var InstanceVersion = context.InstanceVersion;
            ......
        }
```


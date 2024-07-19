---
title: "调用堆栈"
linkTitle: "调用堆栈"
weight: 100
date: 2024-01-19
description: >
  DurableTask client 启动新的实例时的调用堆栈
---



## 调用堆栈概况



```bash
MyDurableFunction2.dll!Company.Function.HelloOrchestration.HttpStart(Microsoft.Azure.Functions.Worker.Http.HttpRequestData req, Microsoft.DurableTask.Client.DurableTaskClient client, Microsoft.Azure.Functions.Worker.FunctionContext executionContext) Line 49 (c:/Users/sky/work/code/durabletask-fork2/MyDurableFunction2/HelloOrchestration.cs:49)
MyDurableFunction2.dll!MyDurableFunction2.DirectFunctionExecutor.ExecuteAsync(Microsoft.Azure.Functions.Worker.FunctionContext context) Line 40 (GeneratedFunctionExecutor.g.cs:40)
Microsoft.Azure.Functions.Worker.Core.dll!Microsoft.Azure.Functions.Worker.Pipeline.FunctionExecutionMiddleware.Invoke(Microsoft.Azure.Functions.Worker.FunctionContext context) Line 20 (FunctionExecutionMiddleware.cs:20)
Microsoft.Azure.Functions.Worker.Core.dll!Microsoft.Extensions.Hosting.MiddlewareWorkerApplicationBuilderExtensions.UseFunctionExecutionMiddleware.AnonymousMethod__1_2(Microsoft.Azure.Functions.Worker.FunctionContext context) Line 57 (WorkerMiddlewareWorkerApplicationBuilderExtensions.cs:57)
Microsoft.Azure.Functions.Worker.Core.dll!Microsoft.Azure.Functions.Worker.OutputBindings.OutputBindingsMiddleware.Invoke(Microsoft.Azure.Functions.Worker.FunctionContext context, Microsoft.Azure.Functions.Worker.Middleware.FunctionExecutionDelegate next) Line 13 (OutputBindingsMiddleware.cs:13)
Microsoft.Azure.Functions.Worker.Core.dll!Microsoft.Extensions.Hosting.MiddlewareWorkerApplicationBuilderExtensions.UseOutputBindingsMiddleware.AnonymousMethod__3(Microsoft.Azure.Functions.Worker.FunctionContext context) Line 84 (WorkerMiddlewareWorkerApplicationBuilderExtensions.cs:84)
Microsoft.Azure.Functions.Worker.Extensions.Http.AspNetCore.dll!Microsoft.Azure.Functions.Worker.Extensions.Http.AspNetCore.FunctionsHttpProxyingMiddleware.Invoke(Microsoft.Azure.Functions.Worker.FunctionContext context, Microsoft.Azure.Functions.Worker.Middleware.FunctionExecutionDelegate next) Line 48 (FunctionsHttpProxyingMiddleware.cs:48)
[Resuming Async Method] (Unknown Source:0)
System.Private.CoreLib.dll!System.Runtime.CompilerServices.AsyncTaskMethodBuilder<System.Threading.Tasks.VoidTaskResult>.AsyncStateMachineBox<Microsoft.Azure.Functions.Worker.Extensions.Http.AspNetCore.FunctionsHttpProxyingMiddleware.<Invoke>d__4>.ExecutionContextCallback(object s) (Unknown Source:0)
System.Private.CoreLib.dll!System.Threading.ExecutionContext.RunInternal(System.Threading.ExecutionContext executionContext, System.Threading.ContextCallback callback, object state) (Unknown Source:0)
System.Private.CoreLib.dll!System.Runtime.CompilerServices.AsyncTaskMethodBuilder<System.Threading.Tasks.VoidTaskResult>.AsyncStateMachineBox<Microsoft.Azure.Functions.Worker.Extensions.Http.AspNetCore.FunctionsHttpProxyingMiddleware.<Invoke>d__4>.MoveNext(System.Threading.Thread threadPoolThread) (Unknown Source:0)
System.Private.CoreLib.dll!System.Runtime.CompilerServices.AsyncTaskMethodBuilder<System.Threading.Tasks.VoidTaskResult>.AsyncStateMachineBox<Microsoft.Azure.Functions.Worker.Extensions.Http.AspNetCore.FunctionsHttpProxyingMiddleware.<Invoke>d__4>.MoveNext() (Unknown Source:0)
System.Private.CoreLib.dll!System.Runtime.CompilerServices.TaskAwaiter.OutputWaitEtwEvents.AnonymousMethod__12_0(System.Action innerContinuation, System.Threading.Tasks.Task innerTask) (Unknown Source:0)
System.Private.CoreLib.dll!System.Threading.Tasks.AwaitTaskContinuation.RunOrScheduleAction(System.Action action, bool allowInlining) (Unknown Source:0)
System.Private.CoreLib.dll!System.Threading.Tasks.Task.RunContinuations(object continuationObject) (Unknown Source:0)
System.Private.CoreLib.dll!System.Threading.Tasks.Task<System.__Canon>.TrySetResult(System.__Canon result) (Unknown Source:0)
System.Private.CoreLib.dll!System.Runtime.CompilerServices.AsyncTaskMethodBuilder<System.__Canon>.SetExistingTaskResult(System.Threading.Tasks.Task<System.__Canon> task, System.__Canon result) (Unknown Source:0)
[Completed] Microsoft.Azure.Functions.Worker.Extensions.Http.AspNetCore.dll!Microsoft.Azure.Functions.Worker.Extensions.Http.AspNetCore.DefaultHttpCoordinator.SetFunctionContextAsync(string invocationId, Microsoft.Azure.Functions.Worker.FunctionContext context) Line 37 (DefaultHttpCoordinator.cs:37)
System.Private.CoreLib.dll!System.Runtime.CompilerServices.AsyncTaskMethodBuilder<System.__Canon>.AsyncStateMachineBox<Microsoft.Azure.Functions.Worker.Extensions.Http.AspNetCore.DefaultHttpCoordinator.<SetFunctionContextAsync>d__3>.ExecutionContextCallback(object s) (Unknown Source:0)
System.Private.CoreLib.dll!System.Threading.ExecutionContext.RunInternal(System.Threading.ExecutionContext executionContext, System.Threading.ContextCallback callback, object state) (Unknown Source:0)
```



## Azure function client sdk code

### GrpcWorkerClientFactory

`Azure/azure-functions-dotnet-worker` 项目下的 GrpcWorkerClientFactory



```c#
            public async Task StartAsync(CancellationToken token)
            {
                if (_running)
                {
                    throw new InvalidOperationException($"The client is already running. Multiple calls to {nameof(StartAsync)} are not supported.");
                }

                _running = true;

                var eventStream = _grpcClient.EventStream(cancellationToken: token);

                await SendStartStreamMessageAsync(eventStream.RequestStream);

                _ = StartWriterAsync(eventStream.RequestStream);
                _ = StartReaderAsync(eventStream.ResponseStream);
            }
```



其中 StartReaderAsync 的实现是：

```c#
            private async Task StartReaderAsync(IAsyncStreamReader<StreamingMessage> responseStream)
            {
                while (await responseStream.MoveNext())
                {
                    await _processor!.ProcessMessageAsync(responseStream.Current);
                }
            }
```





### GrpcWorker

```c#
        Task IMessageProcessor.ProcessMessageAsync(StreamingMessage message)
        {
            // Dispatch and return.
            Task.Run(() => ProcessRequestCoreAsync(message));

            return Task.CompletedTask;
        }
```



后面就是异步操作了：

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





### InvocationHandler



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





### FunctionsApplication



```c#
        public async Task InvokeFunctionAsync(FunctionContext context)
        {
            var scope = new FunctionInvocationScope(context.FunctionDefinition.Name, context.InvocationId);

            using var logScope = _logger.BeginScope(scope);
            using Activity? invokeActivity = _functionActivitySourceFactory.StartInvoke(context);

            try
            {
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







### FunctionsHttpProxyingMiddleware

`Azure/azure-functions-dotnet-worker` 项目下的 OutputBindingsMiddleware

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
            ......
        }
```





### OutputBindingsMiddleware

`Azure/azure-functions-dotnet-worker` 项目下的 OutputBindingsMiddleware

```c#
        public static async Task Invoke(FunctionContext context, FunctionExecutionDelegate next)
        {
            await next(context);

            AddOutputBindings(context);
        }
```

调用  next(context) 方法：

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

这里将调用 `Company.Function.HelloOrchestration.HttpStart()` 方法：

## Customer Code

### HelloOrchestration

以 quickstart `HelloOrchestration.cs` 为例：

```c#
        public static async Task<HttpResponseData> HttpStart(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequestData req,
            [DurableClient] DurableTaskClient client,
            FunctionContext executionContext)
        {
            string instanceId = await client.ScheduleNewOrchestrationInstanceAsync(
                nameof(HelloOrchestration));
            ......
        }
```

为了支持 versioning，这里的 TaskName 需要额外传递 version 参数，因此代码更新为:

```c#
      string instanceId = await client.ScheduleNewOrchestrationInstanceAsync(
        new TaskName(nameof(HelloOrchestration), "1.2.0"));
```

Client 的调用者可以在这里选择传递是否传递新实例的版本。

## Azure functions durable extension的代码

### FunctionsDurableTaskClient

FunctionsDurableTaskClient 的 ScheduleNewOrchestrationInstanceAsync() 实现：

```c#
	private readonly DurableTaskClient inner;    

	public override Task<string> ScheduleNewOrchestrationInstanceAsync(
        TaskName orchestratorName,
        object? input = null,
        StartOrchestrationOptions? options = null,
        CancellationToken cancellation = default)
    {
        return this.inner.ScheduleNewOrchestrationInstanceAsync(orchestratorName, input, options, cancellation);
    }
```

调用了内部的 inner 的 ScheduleNewOrchestrationInstanceAsync()方法, inner 定义的类型是 DurableTaskClient，实际的代码实现是类 GrpcDurableTaskClient



### GrpcDurableTaskClient

GrpcDurableTaskClient 的 ScheduleNewOrchestrationInstanceAsync() 方法实现：

```c#
readonly TaskHubSidecarServiceClient sidecarClient;

public override async Task<string> ScheduleNewOrchestrationInstanceAsync(
        TaskName orchestratorName,
        object? input = null,
        StartOrchestrationOptions? options = null,
        CancellationToken cancellation = default)
    {
        Check.NotEntity(this.options.EnableEntitySupport, options?.InstanceId);

        var request = new P.CreateInstanceRequest
        {
            // 这里的 orchestratorName.Name 的值是 "HelloOrchestration"
            Name = orchestratorName.Name,
            // 这里的 version 是一个 null,现在改为传递  orchestratorName.Version
            Version = orchestratorName.Version,
            InstanceId = options?.InstanceId ?? Guid.NewGuid().ToString("N"),
            // input 为 null
            Input = this.DataConverter.Serialize(input),
        };

        DateTimeOffset? startAt = options?.StartAt;
        this.logger.SchedulingOrchestration(
            request.InstanceId,
            orchestratorName,
            sizeInBytes: request.Input != null ? Encoding.UTF8.GetByteCount(request.Input) : 0,
            startAt.GetValueOrDefault(DateTimeOffset.UtcNow));

        if (startAt.HasValue)
        {
            // Convert timestamps to UTC if not already UTC
            request.ScheduledStartTimestamp = Timestamp.FromDateTimeOffset(startAt.Value.ToUniversalTime());
        }

        P.CreateInstanceResponse? result = await this.sidecarClient.StartInstanceAsync(
            request, cancellationToken: cancellation);
        return result.InstanceId;
    }
```

主要要一路传递 version 字段。

最后调用 sidecarClient 的 StartInstanceAsync() 方法发出 grpc 请求并得到返回的结果。

## Durabletask-dotnet的代码

### TaskHubSidecarServiceClient  

`Microsoft.DurableTask.Protobuf.TaskHubSidecarService.TaskHubSidecarServiceClient`

sidecarClient 定义为 TaskHubSidecarServiceClient  类型，这是根据 protobuf 文件生成的 grpc client 代码：

```c#
      public virtual grpc::AsyncUnaryCall<global::Microsoft.DurableTask.Protobuf.CreateInstanceResponse> StartInstanceAsync(global::Microsoft.DurableTask.Protobuf.CreateInstanceRequest request, grpc::Metadata headers = null, global::System.DateTime? deadline = null, global::System.Threading.CancellationToken cancellationToken = default(global::System.Threading.CancellationToken))
      {
        return StartInstanceAsync(request, new grpc::CallOptions(headers, deadline, cancellationToken));
      }
```

### protobuf 定义

在 Durabletask-dotnet 项目中的`eng\proto\protos\orchestrator_service.proto` 文件中定义了 StartInstance() 方法：

```protobuf
service TaskHubSidecarService {
	......
    // Starts a new orchestration instance.
    rpc StartInstance(CreateInstanceRequest) returns (CreateInstanceResponse);
    ......
}

message CreateInstanceRequest {
    string instanceId = 1;
    string name = 2;
    google.protobuf.StringValue version = 3;
    google.protobuf.StringValue input = 4;
    google.protobuf.Timestamp scheduledStartTimestamp = 5;
    OrchestrationIdReusePolicy orchestrationIdReusePolicy = 6;
}

message CreateInstanceResponse {
    string instanceId = 1;
    string version = 2;
}
```



之后就是 grpc 服务器端的代码实现了。



## azure-functions-durable-extension

### LocalGrpcListener

在 `azure-functions-durable-extension` 仓库下的 `src\WebJobs.Extensions.DurableTask\LocalGrpcListener.cs`  中。



```c#
public async override Task<P.CreateInstanceResponse> StartInstance(P.CreateInstanceRequest request, ServerCallContext context)
            {
                try
                {
                    string instanceId = await this.GetClient(context).StartNewAsync(
                        request.Name, request.InstanceId, request.Version, Raw(request.Input));
                    return new P.CreateInstanceResponse
                    {
                        InstanceId = instanceId,
                    };
                }
                catch (InvalidOperationException)
                {
                    throw new RpcException(new Status(StatusCode.AlreadyExists, $"An Orchestration instance with the ID {request.InstanceId} already exists."));
                }
            }
```

this.GetClient(context) 返回的是 IDurableClient 类型，实际实现为 DurableClient 。

### DurableClient

在 `azure-functions-durable-extension` 仓库下的文件`src\WebJobs.Extensions.DurableTask\ContextImplementations\DurableClient.cs` 中。

```c#
async Task<string> IDurableOrchestrationClient.StartNewAsync<T>(string orchestratorFunctionName, string instanceId, string instanceVersion, T input)
        {
						......

            OrchestrationStatus[] dedupeStatuses = this.GetStatusesNotToOverride();
            Task<OrchestrationInstance> createTask = this.client.CreateOrchestrationInstanceAsync(
                orchestratorFunctionName, instanceVersion, instanceId, input, null, dedupeStatuses);

            this.traceHelper.FunctionScheduled(
                this.TaskHubName,
                orchestratorFunctionName,
                instanceId,
                reason: "NewInstance",
                functionType: FunctionType.Orchestrator,
                isReplay: false);

            OrchestrationInstance instance = await createTask;
            return instance.InstanceId;
        }
```

> versioning： StartNewAsync() 需要增加一个 `string instanceVersion` 参数来传递 version 参数。

这里的 client 是 TaskHubClient 类型



## durabletask

### TaskHubClient

在 `durabletask` 仓库下的文件 `src\DurableTask.Core\TaskHubClient.cs` 中

```c#
        public Task<OrchestrationInstance> CreateOrchestrationInstanceAsync(string name, string version, object input)
        {
            return this.InternalCreateOrchestrationInstanceWithRaisedEventAsync(
                name,
                version,
                orchestrationInstanceId: null,
                input,
                orchestrationTags: null,
                dedupeStatuses: null,
                eventName: null,
                eventData: null);
        }
```

InternalCreateOrchestrationInstanceWithRaisedEventAsync() 方法的实现：

```c#
async Task<OrchestrationInstance> InternalCreateOrchestrationInstanceWithRaisedEventAsync(
            string orchestrationName,
            string orchestrationVersion,
            string orchestrationInstanceId,
            object orchestrationInput,
            IDictionary<string, string> orchestrationTags,
            OrchestrationStatus[] dedupeStatuses,
            string eventName,
            object eventData,
            DateTime? startAt = null)
        {
            TraceContextBase requestTraceContext = null;

            // correlation 
            CorrelationTraceClient.Propagate(()=> { requestTraceContext = CreateOrExtractRequestTraceContext(eventName); });

            if (string.IsNullOrWhiteSpace(orchestrationInstanceId))
            {
                orchestrationInstanceId = Guid.NewGuid().ToString("N");
            }

            var orchestrationInstance = new OrchestrationInstance
            {
                InstanceId = orchestrationInstanceId,
                ExecutionId = Guid.NewGuid().ToString("N"),
            };

            string serializedOrchestrationData = this.defaultConverter.SerializeInternal(orchestrationInput);
            var startedEvent = new ExecutionStartedEvent(-1, serializedOrchestrationData)
            {
                Tags = orchestrationTags,
                Name = orchestrationName,
                Version = orchestrationVersion,
                OrchestrationInstance = orchestrationInstance,
                ScheduledStartTime = startAt
            };

            var startMessage = new TaskMessage
            {
                OrchestrationInstance = orchestrationInstance,
                Event = startedEvent
            };

            this.logHelper.SchedulingOrchestration(startedEvent);

            using Activity newActivity = TraceHelper.StartActivityForNewOrchestration(startedEvent);

            CorrelationTraceClient.Propagate(() => CreateAndTrackDependencyTelemetry(requestTraceContext));

            try
            {
                // Raised events and create orchestration calls use different methods so get handled separately
                await this.ServiceClient.CreateTaskOrchestrationAsync(startMessage, dedupeStatuses);
            }
            catch (Exception e)
            {
                TraceHelper.AddErrorDetailsToSpan(newActivity, e);
                throw;
            }
				
  					......

            return orchestrationInstance;
        }
```

ExecutionStartedEvent 的 version 字段被设置为前面传递过来的 version。

最后调用到

```c#
// Raised events and create orchestration calls use different methods so get handled separately
await this.ServiceClient.CreateTaskOrchestrationAsync(startMessage, dedupeStatuses);
```

this.ServiceClient 的类型是 IOrchestrationServiceClient，实际实现是 AzureStorageDurabilityProvider

## azure-functions-durable-extension

### AzureStorageDurabilityProvider

在 `azure-functions-durable-extension` 仓库的 `src\WebJobs.Extensions.DurableTask\AzureStorageDurabilityProvider.cs` 文件中。

```c#
    internal class AzureStorageDurabilityProvider : DurabilityProvider {}

```



AzureStorageDurabilityProvider 继承自 DurabilityProvider，`src\WebJobs.Extensions.DurableTask\DurabilityProvider.cs`

DurabilityProvider 的 CreateTaskOrchestrationAsync() 方法的实现是：

```c#
        public Task CreateTaskOrchestrationAsync(TaskMessage creationMessage)
        {
            return this.GetOrchestrationServiceClient().CreateTaskOrchestrationAsync(creationMessage);
        }
```

this.GetOrchestrationServiceClient() 返回 IOrchestrationServiceClient 类型，实际实现是 AzureStorageOrchestrationService 。

### AzureStorageOrchestrationService

在 `durabletask` 仓库的 `src\DurableTask.AzureStorage\AzureStorageOrchestrationService.cs` 文件中。



```c#
        public Task CreateTaskOrchestrationAsync(TaskMessage creationMessage)
        {
            return this.CreateTaskOrchestrationAsync(creationMessage, null);
        }
```



```c#
public async Task CreateTaskOrchestrationAsync(TaskMessage creationMessage, OrchestrationStatus[] dedupeStatuses)
        {
            ExecutionStartedEvent executionStartedEvent = creationMessage.Event as ExecutionStartedEvent;
            if (executionStartedEvent == null)
            {
                throw new ArgumentException($"Only {nameof(EventType.ExecutionStarted)} messages are supported.", nameof(creationMessage));
            }

            // Client operations will auto-create the task hub if it doesn't already exist.
            await this.EnsureTaskHubAsync();

            InstanceStatus existingInstance = await this.trackingStore.FetchInstanceStatusAsync(
                creationMessage.OrchestrationInstance.InstanceId);

            if (existingInstance?.State != null && dedupeStatuses != null && dedupeStatuses.Contains(existingInstance.State.OrchestrationStatus))
            {
                // An instance in this state already exists.
                if (this.settings.ThrowExceptionOnInvalidDedupeStatus)
                {
                    throw new OrchestrationAlreadyExistsException($"An Orchestration instance with the status {existingInstance.State.OrchestrationStatus} already exists.");
                }
                
                return;
            }

            if (executionStartedEvent.Generation == null)
            {
                if (existingInstance != null)
                {
                    executionStartedEvent.Generation = existingInstance.State.Generation + 1;
                }
                else
                {
                    executionStartedEvent.Generation = 0;
                }
            }

            ControlQueue controlQueue = await this.GetControlQueueAsync(creationMessage.OrchestrationInstance.InstanceId);
            MessageData startMessage = await this.SendTaskOrchestrationMessageInternalAsync(
                EmptySourceInstance,
                controlQueue,
                creationMessage);

            string inputPayloadOverride = null;
            if (startMessage.CompressedBlobName != null)
            {
                // The input of the orchestration is changed to be a URL to a compressed blob, which
                // is the input queue message. When fetching the orchestration instance status, that
                // blob will be downloaded, decompressed, and the ExecutionStartedEvent.Input value
                // will be returned as the input value.
                inputPayloadOverride = this.messageManager.GetBlobUrl(startMessage.CompressedBlobName);
            }

            await this.trackingStore.SetNewExecutionAsync(
                executionStartedEvent,
                existingInstance?.ETag,
                inputPayloadOverride);
        }
```









## 疑问

TaskHubClient 的 InternalCreateOrchestrationInstanceWithRaisedEventAsync() 方法，除了通过 CreateTaskOrchestrationAsync() 方法发送 ExecutionStartedEvent 外，对于有 eventData 的情况，还需要通过 SendTaskOrchestrationMessageAsync() 方法发送 eventRaisedEvent ：

```c#
            if (eventData != null)
            {
                string serializedEventData = this.defaultConverter.SerializeInternal(eventData);
                var eventRaisedEvent = new EventRaisedEvent(-1, serializedEventData) { Name = eventName };

                this.logHelper.RaisingEvent(orchestrationInstance, eventRaisedEvent);
                
                var eventMessage = new TaskMessage
                {
                    OrchestrationInstance = new OrchestrationInstance
                    {
                        InstanceId = orchestrationInstanceId,

                        // to ensure that the event gets raised on the running
                        // orchestration instance, null the execution id
                        // so that it will find out which execution
                        // it should use for processing
                        ExecutionId = null
                    },
                    Event = eventRaisedEvent,
                };

                await this.ServiceClient.SendTaskOrchestrationMessageAsync(eventMessage);
            }
```

ExecutionStartedEvent 和 EventRaisedEvent 都要被包装为 TaskMessage。






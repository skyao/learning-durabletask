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
            // 这里的 version 是一个 null
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








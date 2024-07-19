---
title: "调用堆栈"
linkTitle: "调用堆栈"
weight: 100
date: 2024-01-19
description: >
  DurableTask worker 运行 activity 的调用堆栈
---



## 调用堆栈概况



```bash
MyDurableFunction2.dll!Company.Function.HelloOrchestration.SayHello(string name, Microsoft.Azure.Functions.Worker.FunctionContext executionContext) Line 35 (c:/Users/sky/work/code/durabletask-fork2/MyDurableFunction2/HelloOrchestration.cs:35)
MyDurableFunction2.dll!MyDurableFunction2.DirectFunctionExecutor.ExecuteAsync(Microsoft.Azure.Functions.Worker.FunctionContext context) Line 36 (GeneratedFunctionExecutor.g.cs:36)
Microsoft.Azure.Functions.Worker.Core.dll!Microsoft.Azure.Functions.Worker.Pipeline.FunctionExecutionMiddleware.Invoke(Microsoft.Azure.Functions.Worker.FunctionContext context) Line 20 (FunctionExecutionMiddleware.cs:20)
Microsoft.Azure.Functions.Worker.Core.dll!Microsoft.Extensions.Hosting.MiddlewareWorkerApplicationBuilderExtensions.UseFunctionExecutionMiddleware.AnonymousMethod__1_2(Microsoft.Azure.Functions.Worker.FunctionContext context) Line 57 (WorkerMiddlewareWorkerApplicationBuilderExtensions.cs:57)
Microsoft.Azure.Functions.Worker.Core.dll!Microsoft.Azure.Functions.Worker.OutputBindings.OutputBindingsMiddleware.Invoke(Microsoft.Azure.Functions.Worker.FunctionContext context, Microsoft.Azure.Functions.Worker.Middleware.FunctionExecutionDelegate next) Line 13 (OutputBindingsMiddleware.cs:13)
Microsoft.Azure.Functions.Worker.Core.dll!Microsoft.Extensions.Hosting.MiddlewareWorkerApplicationBuilderExtensions.UseOutputBindingsMiddleware.AnonymousMethod__3(Microsoft.Azure.Functions.Worker.FunctionContext context) Line 84 (WorkerMiddlewareWorkerApplicationBuilderExtensions.cs:84)
Microsoft.Azure.Functions.Worker.Extensions.Http.AspNetCore.dll!Microsoft.Azure.Functions.Worker.Extensions.Http.AspNetCore.FunctionsHttpProxyingMiddleware.Invoke(Microsoft.Azure.Functions.Worker.FunctionContext context, Microsoft.Azure.Functions.Worker.Middleware.FunctionExecutionDelegate next) Line 34 (FunctionsHttpProxyingMiddleware.cs:34)
Microsoft.Azure.Functions.Worker.Core.dll!Microsoft.Extensions.Hosting.MiddlewareWorkerApplicationBuilderExtensions.UseMiddleware.AnonymousMethod__1(Microsoft.Azure.Functions.Worker.FunctionContext context) Line 105 (WorkerMiddlewareWorkerApplicationBuilderExtensions.cs:105)
Microsoft.Azure.Functions.Worker.Extensions.DurableTask.dll!Microsoft.Azure.Functions.Worker.Extensions.DurableTask.DurableTaskFunctionsMiddleware.Invoke(Microsoft.Azure.Functions.Worker.FunctionContext functionContext, Microsoft.Azure.Functions.Worker.Middleware.FunctionExecutionDelegate next) Line 30 (c:/Users/sky/work/code/durabletask-fork2/azure-functions-durable-extension/src/Worker.Extensions.DurableTask/DurableTaskFunctionsMiddleware.cs:30)
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
```





## azure-function-dotnet-worker



### GrpcWorker



```c#
        Task IMessageProcessor.ProcessMessageAsync(StreamingMessage message)
        {
            // Dispatch and return.
            Task.Run(() => ProcessRequestCoreAsync(message));

            return Task.CompletedTask;
        }
```



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
                    // 走到这里
                    responseMessage.InvocationResponse = await InvocationRequestHandlerAsync(request.InvocationRequest);
                    break;
                ......
```



```c#
        internal Task<InvocationResponse> InvocationRequestHandlerAsync(InvocationRequest request)
        {
            return _invocationHandler.InvokeAsync(request);
        }
```

_invocationHandler 的实现是 InvocationHandler 



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

                // 走到这里
                await _application.InvokeFunctionAsync(context);

                var serializer = _workerOptions.Serializer!;
              ......
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





### DurableTaskFunctionsMiddleware



```c#
    public Task Invoke(FunctionContext functionContext, FunctionExecutionDelegate next)
    {
        if (IsOrchestrationTrigger(functionContext, out BindingMetadata? triggerBinding))
        {
            // call Orchestration 会走到这里
            return RunOrchestrationAsync(functionContext, triggerBinding, next);
        }

        if (IsEntityTrigger(functionContext, out triggerBinding))
        {
            return RunEntityAsync(functionContext, triggerBinding, next);
        }

        // call activity 会走到这里
        return next(functionContext);
    }
```



这里是 call Orchestration 和 call activity 分叉的地方。

`return next(functionContext)` 中的 next 是 `Microsoft.Azure.Functions.Worker.Middleware.FunctionExecutionDelegate` 。

这个 next 是在 MiddlewareWorkerApplicationBuilderExtensions 中通过这样的方式注入的：

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



### FunctionsHttpProxyingMiddleware



```c#
        public async Task Invoke(FunctionContext context, FunctionExecutionDelegate next)
        {
           // Only use the coordinator for HttpTriggers
            if (!_isHttpTrigger.GetOrAdd(context.FunctionId, static (_, c) => IsHttpTriggerFunction(c), context))
            {
              // 代码走到这里
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



### MiddlewareWorkerApplicationBuilderExtensions



```c#
            builder.Use(next =>
            {
                return context =>
                {
                    return OutputBindingsMiddleware.Invoke(context, next);
                };
            });
```

这里有一段内部匿名方法。

### FunctionExecutionMiddleware



```c#
        public Task Invoke(FunctionContext context)
        {
            return _functionExecutor.ExecuteAsync(context).AsTask();
        }
```

_functionExecutor 的实现是 GeneratedFunctionExecutor 。



### GeneratedFunctionExecutor

GeneratedFunctionExecutor 是生成的代码，内容在 GeneratedFunctionExecutor.g.cs 文件中：

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

这里就会进入 SayHello 方法。



FunctionContext, 实际实现是 

`Microsoft.Azure.Functions.Worker.DefaultFunctionContext`

## 用户代码

### activity 实现

```c#
    [Function(nameof(SayHello))]
    public static string SayHello([ActivityTrigger] string name, FunctionContext executionContext)
    {
      ILogger logger = executionContext.GetLogger("SayHello");
      logger.LogInformation("Saying hello to {name}.", name);
      return $"Hello {name}!";
    }
```














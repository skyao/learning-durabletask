---
title: "GrpcWorker"
linkTitle: "GrpcWorker"
weight: 210
date: 2024-01-19
description: >
  DurableTask GrpcWorker
---



`src\DotNetWorker.Grpc\GrpcWorkerClientFactory.cs` 文件中的 GrpcWorkerClient

## 类定义



```c#
         private class GrpcWorkerClient : IWorkerClient
        {
            private readonly FunctionRpcClient _grpcClient;
            private readonly GrpcWorkerStartupOptions _startupOptions;
            private readonly ChannelReader<StreamingMessage> _outputReader;
            private readonly ChannelWriter<StreamingMessage> _outputWriter;
            private bool _running;
            private IMessageProcessor? _processor;
           
         }
```

### 构造函数



```c#
            public GrpcWorkerClient(GrpcHostChannel outputChannel, GrpcWorkerStartupOptions startupOptions, IMessageProcessor processor)
            {
                _startupOptions = startupOptions ?? throw new ArgumentNullException(nameof(startupOptions));
                _processor = processor ?? throw new ArgumentNullException(nameof(processor));

              // 初始化 reader 和 writer，都来自 outputChannel
                _outputReader = outputChannel.Channel.Reader;
                _outputWriter = outputChannel.Channel.Writer;

              // 创建 _grpcClient，FunctionRpcClient 类型
                _grpcClient = CreateClient();
            }
```

CreateClient() 方法：

```c#
private FunctionRpcClient CreateClient()
            {
#if NET5_0_OR_GREATER
                GrpcChannel grpcChannel = GrpcChannel.ForAddress(_startupOptions.HostEndpoint!.AbsoluteUri, new GrpcChannelOptions()
                {
                    MaxReceiveMessageSize = _startupOptions.GrpcMaxMessageLength,
                    MaxSendMessageSize = _startupOptions.GrpcMaxMessageLength,
                    Credentials = ChannelCredentials.Insecure
                });
#else

                var options = new ChannelOption[]
                {
                    new ChannelOption(GrpcCore.ChannelOptions.MaxReceiveMessageLength, _startupOptions.GrpcMaxMessageLength),
                    new ChannelOption(GrpcCore.ChannelOptions.MaxSendMessageLength, _startupOptions.GrpcMaxMessageLength)
                };

                GrpcCore.Channel grpcChannel = new GrpcCore.Channel(_startupOptions.HostEndpoint!.Host, _startupOptions.HostEndpoint.Port, ChannelCredentials.Insecure, options);

#endif
                return new FunctionRpcClient(grpcChannel);
            }
        }
```





## 处理消息

### ProcessMessageAsync



```c#
        Task IMessageProcessor.ProcessMessageAsync(StreamingMessage message)
        {
            // Dispatch and return.
            Task.Run(() => ProcessRequestCoreAsync(message));

            return Task.CompletedTask;
        }
```



### ProcessRequestCoreAsync



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
                    Console.WriteLine("GrpcWorker received WorkerInitRequest");
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



### WorkerInitRequest



```c#
                case MsgType.WorkerInitRequest:
                    responseMessage.WorkerInitResponse = WorkerInitRequestHandler(request.WorkerInitRequest, _workerOptions);
                    break;
```

WorkerInitRequestHandler() 方法的实现：

```c#
        internal static WorkerInitResponse WorkerInitRequestHandler(WorkerInitRequest request, WorkerOptions workerOptions)
        {
            var response = new WorkerInitResponse
            {
                Result = new StatusResult { Status = StatusResult.Types.Status.Success },
                WorkerVersion = WorkerInformation.Instance.WorkerVersion,
                WorkerMetadata = GetWorkerMetadata()
            };

            response.Capabilities.Add(GetWorkerCapabilities(workerOptions));

            return response;
        }
```

> TBD: WorkerVersion 怎么来的？



### InvocationRequest



```c#
                case MsgType.InvocationRequest:
                    responseMessage.InvocationResponse = await InvocationRequestHandlerAsync(request.InvocationRequest);
                    break;
```

InvocationRequestHandlerAsync 方法的实现：

```c#
        internal Task<InvocationResponse> InvocationRequestHandlerAsync(InvocationRequest request)
        {
            return _invocationHandler.InvokeAsync(request);
        }
```


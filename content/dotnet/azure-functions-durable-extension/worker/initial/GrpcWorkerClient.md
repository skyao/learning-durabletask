---
title: "GrpcWorkerClient"
linkTitle: "GrpcWorkerClient"
weight: 200
date: 2024-01-19
description: >
  DurableTask GrpcWorkerClient
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





## start过程

### StartAsync



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


---
title: "过时的初始化"
linkTitle: "过时的"
weight: 2
date: 2024-01-19
description: >
  DurableTask worker 已经过时的初始化过程
---



## GrpcDurableTaskWorker

~~仓库~~ `durabletask-dotnet` 下的文件 `src\Worker\Grpc\GrpcDurableTaskWorker.Processor.cs`

```c#
 public async Task ExecuteAsync(CancellationToken cancellation)
        {
            while (!cancellation.IsCancellationRequested)
            {
                try
                {
                    AsyncServerStreamingCall<P.WorkItem> stream = await this.ConnectAsync(cancellation);
                    await this.ProcessWorkItemsAsync(stream, cancellation);
                }
                ......
            }
        }
```

ConnectAsync() 方法中会调用 grpc protobuf 文件中定义的 GetWorkItems() 方法：

```c#
        async Task<AsyncServerStreamingCall<P.WorkItem>> ConnectAsync(CancellationToken cancellation)
        {
            await this.sidecar!.HelloAsync(EmptyMessage, cancellationToken: cancellation);
            this.Logger.EstablishedWorkItemConnection();

            Console.WriteLine("********GrpcDurableTaskWorker call GetWorkItems()********");

            // Get the stream for receiving work-items
            return this.sidecar!.GetWorkItems(new P.GetWorkItemsRequest(), cancellationToken: cancellation);
        }
```

这本该是 worker 正常的初始化流程，但现在已经被废弃。

> 备注：记录一下避免误解，事实上我被耽误了很多时间在这里。


---
title: "调用堆栈"
linkTitle: "调用堆栈"
weight: 100
date: 2024-01-19
description: >
  DurableTask client 启动新的实例时的调用堆栈
---



## 调用堆栈概况



 在 `azure-functions-dotnet-worker` 仓库下的  `src\DotNetWorker.Grpc\GrpcWorker.cs` 中增加日志打印:

```c#
        public Task StartAsync(CancellationToken token)
        {
            Console.WriteLine(new System.Diagnostics.StackTrace(true));

            _workerClient = _workerClientFactory.CreateClient(this);

            Console.WriteLine("_workerClient is " + _workerClient.GetType().Name);

            return _workerClient.StartAsync(token);
        }
```

得到启动时初始化 worker 的调用堆栈：

```c#
[2024-04-08T09:07:04.591Z]    at Microsoft.Azure.Functions.Worker.GrpcWorker.StartAsync(CancellationToken token) in C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\src\DotNetWorker.Grpc\GrpcWorker.cs:line 58
[2024-04-08T09:07:04.592Z]    at Microsoft.Azure.Functions.Worker.WorkerHostedService.StartAsync(CancellationToken cancellationToken) in C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\src\DotNetWorker.Core\WorkerHostedService.cs:line 25
[2024-04-08T09:07:04.592Z]    at System.Runtime.CompilerServices.AsyncMethodBuilderCore.Start[TStateMachine](TStateMachine& stateMachine)
[2024-04-08T09:07:04.593Z]    at Microsoft.Azure.Functions.Worker.WorkerHostedService.StartAsync(CancellationToken cancellationToken)
[2024-04-08T09:07:04.593Z]    at Microsoft.Extensions.Hosting.Internal.Host.<StartAsync>b__15_1(IHostedService service, CancellationToken token)
[2024-04-08T09:07:04.593Z]    at System.Runtime.CompilerServices.AsyncMethodBuilderCore.Start[TStateMachine](TStateMachine& stateMachine)
[2024-04-08T09:07:04.593Z]    at Microsoft.Extensions.Hosting.Internal.Host.<StartAsync>b__15_1(IHostedService service, CancellationToken token)
[2024-04-08T09:07:04.594Z]    at Microsoft.Extensions.Hosting.Internal.Host.ForeachService[T](IEnumerable`1 services, CancellationToken token, Boolean concurrent, Boolean abortOnFirstException, List`1 exceptions, Func`3 operation)
[2024-04-08T09:07:04.594Z]    at System.Runtime.CompilerServices.AsyncMethodBuilderCore.Start[TStateMachine](TStateMachine& stateMachine)
[2024-04-08T09:07:04.594Z]    at Microsoft.Extensions.Hosting.Internal.Host.ForeachService[T](IEnumerable`1 services, CancellationToken token, Boolean concurrent, Boolean abortOnFirstException, List`1 exceptions, Func`3 operation)
[2024-04-08T09:07:04.594Z]    at Microsoft.Extensions.Hosting.Internal.Host.StartAsync(CancellationToken cancellationToken)
[2024-04-08T09:07:04.595Z]    at System.Runtime.CompilerServices.AsyncMethodBuilderCore.Start[TStateMachine](TStateMachine& stateMachine)
[2024-04-08T09:07:04.595Z]    at Microsoft.Extensions.Hosting.Internal.Host.StartAsync(CancellationToken cancellationToken)
[2024-04-08T09:07:04.595Z]    at Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync(IHost host, CancellationToken token)
[2024-04-08T09:07:04.596Z]    at System.Runtime.CompilerServices.AsyncMethodBuilderCore.Start[TStateMachine](TStateMachine& stateMachine)
[2024-04-08T09:07:04.596Z]    at Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync(IHost host, CancellationToken token)
[2024-04-08T09:07:04.596Z]    at Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run(IHost host)
[2024-04-08T09:07:04.596Z]    at Program.<Main>$(String[] args) in C:\Users\sky\work\code\durabletask\MyDurableFunction1\Program.cs:line 13
    
......
[2024-04-08T09:07:04.598Z] _workerClient is GrpcWorkerClient
[2024-04-08T09:07:04.629Z] Worker process started and initialized.
```





## 其他



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


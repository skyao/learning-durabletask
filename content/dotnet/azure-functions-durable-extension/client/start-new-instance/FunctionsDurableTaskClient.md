---
title: "FunctionsDurableTaskClient"
linkTitle: "FunctionsDurableTaskClient"
weight: 200
date: 2024-01-19
description: >
  FunctionsDurableTaskClient的源码
---



client 调用的开始：

```c#
        public static async Task<HttpResponseData> HttpStart(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequestData req,
            [DurableClient] DurableTaskClient client,
            FunctionContext executionContext)
        {
          ......
string instanceId = await client.ScheduleNewOrchestrationInstanceAsync(
                nameof(HelloOrchestration));
          ......
        }
```

client 类型是 DurableTaskClient，实际实现的类是 FunctionsDurableTaskClient。代码在 azure-functions-durable-extension 仓库的 `src\Worker.Extensions.DurableTask\FunctionsDurableTaskClient.cs` 文件中



## 类定义和构造函数

```c#
internal sealed class FunctionsDurableTaskClient : DurableTaskClient
{
    private readonly DurableTaskClient inner;

    public FunctionsDurableTaskClient(DurableTaskClient inner, string? queryString)
        : base(inner.Name)
    {
        this.inner = inner;
        this.QueryString = queryString;
    }
  
```



Debug 看到这个 inner 的实现是 `Microsoft.DurableTask.Client.Grpc.GrpcDurableTaskClient`



## ScheduleNewOrchestrationInstanceAsync()



```c#
    public override Task<string> ScheduleNewOrchestrationInstanceAsync(
        TaskName orchestratorName,
        object? input = null,
        StartOrchestrationOptions? options = null,
        CancellationToken cancellation = default)
    {
        return this.inner.ScheduleNewOrchestrationInstanceAsync(orchestratorName, input, options, cancellation);
    }
```





#### TaskName

TaskName 的代码在 durabletask-dotnet 仓库下的`src\Abstractions\TaskName.cs`

```c#
public readonly struct TaskName : IEquatable<TaskName>
```


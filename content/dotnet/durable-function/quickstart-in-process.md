---
title: "durable functions quickstart"
linkTitle: "quickstart"
weight: 100
date: 2024-01-19
description: >
  durable functions quickstart
---



参考：

- [Create your first durable function in Azure using C# | Microsoft Learn](https://learn.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-create-first-csharp?pivots=code-editor-vscode)

## 文档

Durable Functions 是 Azure Functions 的扩展，可让您在无服务器环境中编写有状态函数。该扩展可为您管理状态、检查点和重启。

在本文中，您将学习如何使用 Visual Studio Code 本地创建和测试 "hello world "持久函数。该函数协调和连锁调用其他函数。然后，您就可以将函数代码发布到 Azure。这些工具是 Visual Studio Code Azure 函数扩展的一部分。

### 前提条件

要完成本教程

- 安装 Visual Studio Code。

- 安装以下 Visual Studio Code 扩展：

  - Azure 函数

  - C#

- 确保拥有最新版本的 Azure Functions 核心工具。

- Durable Functions 需要一个 Azure 存储账户。你需要一个 Azure 订阅。

- 确保安装了 3.1 版或更高版本的 .NET Core SDK。



## 实操



### 准备工作

#### 安装 vs code 插件

- Azure Functions

- C#

#### 安装 Azure Functions Core Tools

https://go.microsoft.com/fwlink/?linkid=2174087 

安装完成之后，用 windows 的 cmd 就可以使用 func 命令了：

```bash
func --version
4.0.5611
```

此时用 git bash 或者 zsh 也能使用 func 命令:

```bash
which func
/c/Program Files/Microsoft/Azure Functions Core Tools/func
func --version
4.0.5611
```

但是，vs code 下的 shell 不能访问 func。需要 修改 zsh 

```bash
vi ~/.zshrc
```

加入 PATH

```bash
# azure functions
export PATH=$PATH:"/c/Program Files/Microsoft/Azure Functions Core Tools/"
```

再 `source  ~/.zshrc` 就可以了。



### 创建本地项目

参考：

https://learn.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-create-first-csharp?pivots=code-editor-vscode#create-an-azure-functions-project



### 添加 function 到应用



https://learn.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-create-first-csharp?pivots=code-editor-vscode#add-functions-to-the-app

报错：

```bash
Detected package downgrade: Microsoft.Azure.Functions.Worker.Extensions.Http from 3.1.0 to 3.0.13. Reference the package directly from the project to select a different version.  
```

修改 `mydurablefunctions1.csproj` 文件，将

```xml
<PackageReference Include="Microsoft.Azure.Functions.Worker.Extensions.Http" Version="3.0.13" />
```

修改为：

```xml
<PackageReference Include="Microsoft.Azure.Functions.Worker.Extensions.Http" Version="3.1.0" />
```

关闭 vs code，再重新打开这个项目。

然后就是弹出 select a storage account 时，选择了  azure storage account，但是很奇怪没有弹出文档中所说的各种提示。导致无法按照文档操作，只能手工创建  azure storage account



### 手工创建 azure storage account

打开 azure 页面，手工创建 azure storage account：

- resource group:  versioning-dev

- storage account name:  skyversioningdev

- Region: east asia

创建完成后，打开 "Accdess keys"，就能看到key，类似：

```bash
DefaultEndpointsProtocol=https;AccountName=skyversioningdev;AccountKey=5dx xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxEvy2+AStFSJ8zg==;EndpointSuffix=core.windows.net
```

修改 local.settings.json 文件，将原本为空的 AzureWebJobsStorage 设置为：

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=skyversioningdev;AccountKey=5dx xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxEvy2+AStFSJ8zg==;EndpointSuffix=core.windows.net",
    "FUNCTIONS_WORKER_RUNTIME": "dotnet-isolated"
  }
}
```

### 手工添加 DurableTask package 

> 备注：忘了为什么要加这个依赖了，奇怪

然后再手工添加 DurableTask package 到项目中，执行命令：

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.DurableTask --version 2.13.1
```

这时打开 mydurablefunctions1.csproj 就能看到了：

```xml
  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.Azure.Functions.Worker" Version="1.20.1" />
    <PackageReference Include="Microsoft.Azure.Functions.Worker.Extensions.DurableTask" Version="1.0.0" />
    <PackageReference Include="Microsoft.Azure.Functions.Worker.Extensions.Http" Version="3.1.0" />
    <PackageReference Include="Microsoft.Azure.Functions.Worker.Extensions.Http.AspNetCore" Version="1.2.0" />
    <PackageReference Include="Microsoft.Azure.Functions.Worker.Sdk" Version="1.16.4" />
    <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.21.0" />
    <PackageReference Include="Microsoft.Azure.Functions.Worker.ApplicationInsights" Version="1.1.0" />
    <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.DurableTask" Version="2.13.1" />
  </ItemGroup>
```

### debug

修改 .vscode\launch.json ，增加 `justMyCode=false` 的配置：

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "HelloOrchestration",
      "type": "coreclr",
      "request": "attach",
      "processId": "${command:azureFunctions.pickProcess}",
      "justMyCode": false,
    }
  ]
}
```



备注：

- 如果启动 debug 时遇到报错，提示 `Failed to find "func: host start" task.` ，请重启 vs code 再试

### 改用 async api 并修改依赖版本

运行时报错：

```bash
Azure Functions Core Tools
Core Tools Version:       4.0.5611 Commit hash: N/A +591b8aec842e333a87ea9e23ba390bb5effe0655 (64-bit)
Function Runtime Version: 4.31.1.22191

[2024-04-12T06:48:22.103Z] Found C:\Users\sky\work\code\durabletask-fork2\MyDurableFunction2\MyDurableFunction2.csproj. Using for user secrets file configuration.
[2024-04-12T06:48:24.667Z] Worker process started and initialized.

Functions:

        HelloOrchestration_HttpStart: [GET,POST] http://localhost:7071/api/HelloOrchestration_HttpStart

        HelloOrchestration: orchestrationTrigger

        SayHello: activityTrigger

For detailed output, run func with --verbose flag.
[2024-04-12T06:48:30.227Z] Host lock lease acquired by instance ID '00000000000000000000000000EA9F9B'.
[2024-04-12T06:49:31.664Z] Executing 'Functions.HelloOrchestration_HttpStart' (Reason='This function was programmatically called via the host APIs.', Id=8785cd2a-89e9-496c-bb9b-6cfcb7be39be)
[2024-04-12T06:49:32.010Z] Scheduling new HelloOrchestration orchestration with instance ID '94153d04204a4081802ebaa3642210c3' and 0 bytes of input data.
[2024-04-12T06:49:32.365Z] Started orchestration with ID = '94153d04204a4081802ebaa3642210c3'.
[2024-04-12T06:49:32.432Z] Function 'HelloOrchestration_HttpStart', Invocation id '8785cd2a-89e9-496c-bb9b-6cfcb7be39be': An exception was thrown by the invocation.
[2024-04-12T06:49:32.432Z] Result: Function 'HelloOrchestration_HttpStart', Invocation id '8785cd2a-89e9-496c-bb9b-6cfcb7be39be': An exception was thrown by the invocation.
Exception: System.InvalidOperationException: Synchronous operations are disallowed. Call WriteAsync or set AllowSynchronousIO to true instead.
[2024-04-12T06:49:32.433Z]    at Microsoft.AspNetCore.Server.Kestrel.Core.Internal.Http.HttpResponseStream.Write(Byte[] buffer, Int32 offset, Int32 count)
[2024-04-12T06:49:32.433Z]    at Azure.Core.Serialization.JsonObjectSerializer.Serialize(Stream stream, Object value, Type inputType, CancellationToken 
cancellationToken)
[2024-04-12T06:49:32.434Z]    at Microsoft.Azure.Functions.Worker.DurableTaskClientExtensions.CreateCheckStatusResponse(DurableTaskClient client, HttpRequestData request, String instanceId, HttpStatusCode statusCode, CancellationToken cancellation) in /_/src/Worker.Extensions.DurableTask/DurableTaskClientExtensions.cs:line 97
[2024-04-12T06:49:32.435Z]    at Microsoft.Azure.Functions.Worker.DurableTaskClientExtensions.CreateCheckStatusResponse(DurableTaskClient client, HttpRequestData request, String instanceId, CancellationToken cancellation) in /_/src/Worker.Extensions.DurableTask/DurableTaskClientExtensions.cs:line 34    
[2024-04-12T06:49:32.435Z]    at Company.Function.HelloOrchestration.HttpStart(HttpRequestData req, DurableTaskClient client, FunctionContext executionContext) in C:\Users\sky\work\code\durabletask-fork2\MyDurableFunction2\HelloOrchestration.cs:line 52
[2024-04-12T06:49:32.435Z]    at MyDurableFunction2.DirectFunctionExecutor.ExecuteAsync(FunctionContext context) in C:\Users\sky\work\code\durabletask-fork2\MyDurableFunction2\Microsoft.Azure.Functions.Worker.Sdk.Generators\Microsoft.Azure.Functions.Worker.Sdk.Generators.FunctionExecutorGenerator\GeneratedFunctionExecutor.g.cs:line 40
[2024-04-12T06:49:32.436Z]    at Microsoft.Azure.Functions.Worker.OutputBindings.OutputBindingsMiddleware.Invoke(FunctionContext context, FunctionExecutionDelegate next) in D:\a\_work\1\s\src\DotNetWorker.Core\OutputBindings\OutputBindingsMiddleware.cs:line 13
[2024-04-12T06:49:32.437Z]    at Microsoft.Azure.Functions.Worker.Extensions.Http.AspNetCore.FunctionsHttpProxyingMiddleware.Invoke(FunctionContext context, FunctionExecutionDelegate next) in D:\a\_work\1\s\extensions\Worker.Extensions.Http.AspNetCore\src\FunctionsMiddleware\FunctionsHttpProxyingMiddleware.cs:line 48
[2024-04-12T06:49:32.437Z]    at Microsoft.Azure.Functions.Worker.Extensions.DurableTask.DurableTaskFunctionsMiddleware.Invoke(FunctionContext functionContext, FunctionExecutionDelegate next) in /_/src/Worker.Extensions.DurableTask/DurableTaskFunctionsMiddleware.cs:line 22
[2024-04-12T06:49:32.438Z]    at Microsoft.Azure.Functions.Worker.FunctionsApplication.InvokeFunctionAsync(FunctionContext context) in D:\a\_work\1\s\src\DotNetWorker.Core\FunctionsApplication.cs:line 77
Stack:    at Microsoft.AspNetCore.Server.Kestrel.Core.Internal.Http.HttpResponseStream.Write(Byte[] buffer, Int32 offset, Int32 count)
[2024-04-12T06:49:32.438Z]    at Azure.Core.Serialization.JsonObjectSerializer.Serialize(Stream stream, Object value, Type inputType, CancellationToken 
cancellationToken)
[2024-04-12T06:49:32.439Z]    at Microsoft.Azure.Functions.Worker.DurableTaskClientExtensions.CreateCheckStatusResponse(DurableTaskClient client, HttpRequestData request, String instanceId, HttpStatusCode statusCode, CancellationToken cancellation) in /_/src/Worker.Extensions.DurableTask/DurableTaskClientExtensions.cs:line 97
[2024-04-12T06:49:32.439Z]    at Microsoft.Azure.Functions.Worker.DurableTaskClientExtensions.CreateCheckStatusResponse(DurableTaskClient client, HttpRequestData request, String instanceId, CancellationToken cancellation) in /_/src/Worker.Extensions.DurableTask/DurableTaskClientExtensions.cs:line 34    
[2024-04-12T06:49:32.440Z]    at Company.Function.HelloOrchestration.HttpStart(HttpRequestData req, DurableTaskClient client, FunctionContext executionContext) in C:\Users\sky\work\code\durabletask-fork2\MyDurableFunction2\HelloOrchestration.cs:line 52
[2024-04-12T06:49:32.440Z]    at MyDurableFunction2.DirectFunctionExecutor.ExecuteAsync(FunctionContext context) in C:\Users\sky\work\code\durabletask-fork2\MyDurableFunction2\Microsoft.Azure.Functions.Worker.Sdk.Generators\Microsoft.Azure.Functions.Worker.Sdk.Generators.FunctionExecutorGenerator\GeneratedFunctionExecutor.g.cs:line 40
[2024-04-12T06:49:32.441Z]    at Microsoft.Azure.Functions.Worker.OutputBindings.OutputBindingsMiddleware.Invoke(FunctionContext context, FunctionExecutionDelegate next) in D:\a\_work\1\s\src\DotNetWorker.Core\OutputBindings\OutputBindingsMiddleware.cs:line 13
[2024-04-12T06:49:32.441Z]    at Microsoft.Azure.Functions.Worker.Extensions.Http.AspNetCore.FunctionsHttpProxyingMiddleware.Invoke(FunctionContext context, FunctionExecutionDelegate next) in D:\a\_work\1\s\extensions\Worker.Extensions.Http.AspNetCore\src\FunctionsMiddleware\FunctionsHttpProxyingMiddleware.cs:line 48
[2024-04-12T06:49:32.442Z]    at Microsoft.Azure.Functions.Worker.Extensions.DurableTask.DurableTaskFunctionsMiddleware.Invoke(FunctionContext functionContext, FunctionExecutionDelegate next) in /_/src/Worker.Extensions.DurableTask/DurableTaskFunctionsMiddleware.cs:line 22
[2024-04-12T06:49:32.443Z]    at Microsoft.Azure.Functions.Worker.FunctionsApplication.InvokeFunctionAsync(FunctionContext context) in D:\a\_work\1\s\src\DotNetWorker.Core\FunctionsApplication.cs:line 77.
[2024-04-12T06:49:32.484Z] Executed 'Functions.HelloOrchestration_HttpStart' (Failed, Id=8785cd2a-89e9-496c-bb9b-6cfcb7be39be, Duration=840ms)
[2024-04-12T06:49:32.485Z] System.Private.CoreLib: Exception while executing function: Functions.HelloOrchestration_HttpStart. System.Private.CoreLib: Result: Failure
Exception: System.InvalidOperationException: Synchronous operations are disallowed. Call WriteAsync or set AllowSynchronousIO to true instead.
[2024-04-12T06:49:32.485Z]    at Microsoft.AspNetCore.Server.Kestrel.Core.Internal.Http.HttpResponseStream.Write(Byte[] buffer, Int32 offset, Int32 count)
[2024-04-12T06:49:32.485Z]    at Azure.Core.Serialization.JsonObjectSerializer.Serialize(Stream stream, Object value, Type inputType, CancellationToken 
cancellationToken)
[2024-04-12T06:49:32.486Z]    at Microsoft.Azure.Functions.Worker.DurableTaskClientExtensions.CreateCheckStatusResponse(DurableTaskClient client, HttpRequestData request, String instanceId, HttpStatusCode statusCode, CancellationToken cancellation) in /_/src/Worker.Extensions.DurableTask/DurableTaskClientExtensions.cs:line 97
[2024-04-12T06:49:32.486Z]    at Microsoft.Azure.Functions.Worker.DurableTaskClientExtensions.CreateCheckStatusResponse(DurableTaskClient client, HttpRequestData request, String instanceId, CancellationToken cancellation) in /_/src/Worker.Extensions.DurableTask/DurableTaskClientExtensions.cs:line 34    
[2024-04-12T06:49:32.486Z]    at Company.Function.HelloOrchestration.HttpStart(HttpRequestData req, DurableTaskClient client, FunctionContext executionContext) in C:\Users\sky\work\code\durabletask-fork2\MyDurableFunction2\HelloOrchestration.cs:line 52
[2024-04-12T06:49:32.486Z]    at MyDurableFunction2.DirectFunctionExecutor.ExecuteAsync(FunctionContext context) in C:\Users\sky\work\code\durabletask-fork2\MyDurableFunction2\Microsoft.Azure.Functions.Worker.Sdk.Generators\Microsoft.Azure.Functions.Worker.Sdk.Generators.FunctionExecutorGenerator\GeneratedFunctionExecutor.g.cs:line 40
[2024-04-12T06:49:32.487Z]    at Microsoft.Azure.Functions.Worker.OutputBindings.OutputBindingsMiddleware.Invoke(FunctionContext context, FunctionExecutionDelegate next) in D:\a\_work\1\s\src\DotNetWorker.Core\OutputBindings\OutputBindingsMiddleware.cs:line 13
[2024-04-12T06:49:32.487Z]    at Microsoft.Azure.Functions.Worker.Extensions.Http.AspNetCore.FunctionsHttpProxyingMiddleware.Invoke(FunctionContext context, FunctionExecutionDelegate next) in D:\a\_work\1\s\extensions\Worker.Extensions.Http.AspNetCore\src\FunctionsMiddleware\FunctionsHttpProxyingMiddleware.cs:line 48
[2024-04-12T06:49:32.487Z]    at Microsoft.Azure.Functions.Worker.Extensions.DurableTask.DurableTaskFunctionsMiddleware.Invoke(FunctionContext functionContext, FunctionExecutionDelegate next) in /_/src/Worker.Extensions.DurableTask/DurableTaskFunctionsMiddleware.cs:line 22
[2024-04-12T06:49:32.488Z]    at Microsoft.Azure.Functions.Worker.FunctionsApplication.InvokeFunctionAsync(FunctionContext context) in D:\a\_work\1\s\src\DotNetWorker.Core\FunctionsApplication.cs:line 77
[2024-04-12T06:49:32.488Z]    at Microsoft.Azure.Functions.Worker.Handlers.InvocationHandler.InvokeAsync(InvocationRequest request) in D:\a\_work\1\s\src\DotNetWorker.Grpc\Handlers\InvocationHandler.cs:line 88
Stack:    at Microsoft.AspNetCore.Server.Kestrel.Core.Internal.Http.HttpResponseStream.Write(Byte[] buffer, Int32 offset, Int32 count)
[2024-04-12T06:49:32.488Z]    at Azure.Core.Serialization.JsonObjectSerializer.Serialize(Stream stream, Object value, Type inputType, CancellationToken 
cancellationToken)
[2024-04-12T06:49:32.489Z]    at Microsoft.Azure.Functions.Worker.DurableTaskClientExtensions.CreateCheckStatusResponse(DurableTaskClient client, HttpRequestData request, String instanceId, HttpStatusCode statusCode, CancellationToken cancellation) in /_/src/Worker.Extensions.DurableTask/DurableTaskClientExtensions.cs:line 97
[2024-04-12T06:49:32.489Z]    at Microsoft.Azure.Functions.Worker.DurableTaskClientExtensions.CreateCheckStatusResponse(DurableTaskClient client, HttpRequestData request, String instanceId, CancellationToken cancellation) in /_/src/Worker.Extensions.DurableTask/DurableTaskClientExtensions.cs:line 34    
[2024-04-12T06:49:32.489Z]    at Company.Function.HelloOrchestration.HttpStart(HttpRequestData req, DurableTaskClient client, FunctionContext executionContext) in C:\Users\sky\work\code\durabletask-fork2\MyDurableFunction2\HelloOrchestration.cs:line 52
[2024-04-12T06:49:32.490Z]    at MyDurableFunction2.DirectFunctionExecutor.ExecuteAsync(FunctionContext context) in C:\Users\sky\work\code\durabletask-fork2\MyDurableFunction2\Microsoft.Azure.Functions.Worker.Sdk.Generators\Microsoft.Azure.Functions.Worker.Sdk.Generators.FunctionExecutorGenerator\GeneratedFunctionExecutor.g.cs:line 40
[2024-04-12T06:49:32.490Z]    at Microsoft.Azure.Functions.Worker.OutputBindings.OutputBindingsMiddleware.Invoke(FunctionContext context, FunctionExecutionDelegate next) in D:\a\_work\1\s\src\DotNetWorker.Core\OutputBindings\OutputBindingsMiddleware.cs:line 13
[2024-04-12T06:49:32.490Z]    at Microsoft.Azure.Functions.Worker.Extensions.Http.AspNetCore.FunctionsHttpProxyingMiddleware.Invoke(FunctionContext context, FunctionExecutionDelegate next) in D:\a\_work\1\s\extensions\Worker.Extensions.Http.AspNetCore\src\FunctionsMiddleware\FunctionsHttpProxyingMiddleware.cs:line 48
[2024-04-12T06:49:32.491Z]    at Microsoft.Azure.Functions.Worker.Extensions.DurableTask.DurableTaskFunctionsMiddleware.Invoke(FunctionContext functionContext, FunctionExecutionDelegate next) in /_/src/Worker.Extensions.DurableTask/DurableTaskFunctionsMiddleware.cs:line 22
[2024-04-12T06:49:32.491Z]    at Microsoft.Azure.Functions.Worker.FunctionsApplication.InvokeFunctionAsync(FunctionContext context) in D:\a\_work\1\s\src\DotNetWorker.Core\FunctionsApplication.cs:line 77
[2024-04-12T06:49:32.491Z]    at Microsoft.Azure.Functions.Worker.Handlers.InvocationHandler.InvokeAsync(InvocationRequest request) in D:\a\_work\1\s\src\DotNetWorker.Grpc\Handlers\InvocationHandler.cs:line 88.
```

意思是自动生成的代码中:

```c#
return client.CreateCheckStatusResponse(req, instanceId);
```

这里不应该用同步（sync）api，而应该改用异步 （async）api：

```bash
Exception: System.InvalidOperationException: Synchronous operations are disallowed. Call WriteAsync or set AllowSynchronousIO to true instead.

```

将这段代码修改为:

```c#
return await client.CreateCheckStatusResponseAsync(req, instanceId);
```

编译失败，没有这个API，这是 Microsoft.Azure.Functions.Worker.Extensions.DurableTask 依赖版本太低的问题。修改 MyDurableFunction1.csproj 文件：

```xml
    <PackageReference Include="Microsoft.Azure.Functions.Worker.Extensions.DurableTask" Version="1.1.2" />
```

这下终于可以正常的跑起来这个 quickstart 了。



```bash
[2024-04-12T07:02:33.790Z] Executing 'Functions.HelloOrchestration_HttpStart' (Reason='This function was programmatically called via the host APIs.', Id=fa30ce71-7f16-4dd7-a640-df39f29158e7)
[2024-04-12T07:02:34.055Z] Scheduling new HelloOrchestration orchestration with instance ID '1a01031830e8409eaa09d14b27c7e681' and 0 bytes of input data.
[2024-04-12T07:02:34.716Z] Host lock lease acquired by instance ID '00000000000000000000000000EA9F9B'.
[2024-04-12T07:02:34.831Z] Started orchestration with ID = '1a01031830e8409eaa09d14b27c7e681'.
[2024-04-12T07:02:34.864Z] Executed 'Functions.HelloOrchestration_HttpStart' (Succeeded, Id=fa30ce71-7f16-4dd7-a640-df39f29158e7, Duration=1095ms)
[2024-04-12T07:02:39.126Z] Executing 'Functions.HelloOrchestration' (Reason='(null)', Id=c02f27dd-a472-448d-a3d9-918cae4febbf)
[2024-04-12T07:02:39.319Z] Saying hello.
[2024-04-12T07:02:39.335Z] Executed 'Functions.HelloOrchestration' (Succeeded, Id=c02f27dd-a472-448d-a3d9-918cae4febbf, Duration=215ms)
[2024-04-12T07:02:39.516Z] Executing 'Functions.SayHello' (Reason='(null)', Id=3ff9d7fa-1b1a-4ae6-8846-d41670f3c53d)
[2024-04-12T07:02:39.524Z] Saying hello to Tokyo.
[2024-04-12T07:02:39.525Z] Executed 'Functions.SayHello' (Succeeded, Id=3ff9d7fa-1b1a-4ae6-8846-d41670f3c53d, Duration=10ms)
[2024-04-12T07:02:39.894Z] Executing 'Functions.HelloOrchestration' (Reason='(null)', Id=6ef4aea3-2366-4dcc-951c-b96e43fd62a2)
[2024-04-12T07:02:39.906Z] Executed 'Functions.HelloOrchestration' (Succeeded, Id=6ef4aea3-2366-4dcc-951c-b96e43fd62a2, Duration=12ms)
[2024-04-12T07:02:40.016Z] Executing 'Functions.SayHello' (Reason='(null)', Id=b232d3c6-98d7-4299-9224-ff7a87d9ad9c)
[2024-04-12T07:02:40.042Z] Saying hello to Seattle.
[2024-04-12T07:02:40.043Z] Executed 'Functions.SayHello' (Succeeded, Id=b232d3c6-98d7-4299-9224-ff7a87d9ad9c, Duration=28ms)
[2024-04-12T07:02:40.424Z] Executing 'Functions.HelloOrchestration' (Reason='(null)', Id=d7dd4d04-1b1c-41e2-b47f-51459792cd4a)
[2024-04-12T07:02:40.442Z] Executed 'Functions.HelloOrchestration' (Succeeded, Id=d7dd4d04-1b1c-41e2-b47f-51459792cd4a, Duration=19ms)
[2024-04-12T07:02:40.614Z] Executing 'Functions.SayHello
```




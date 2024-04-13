---
title: "构建项目"
linkTitle: "build"
weight: 100
date: 2024-01-19
description: >
  构建 azure-functions-dotnet-worker 项目
---

https://github.com/Azure/azure-functions-dotnet-worker

##  获取源码

```bash
# cd ~/work/code/durabletask-fork
git clone git@github.com:Azure/azure-functions-dotnet-worker.git
```

## 执行 build



在终端执行命令：

```bash
# cd ~/work/code/durabletask-fork/azure-functions-dotnet-worker
dotnet build
```

输出如下：

```bash
dotnet build
适用于 .NET MSBuild 版本 17.9.6+a4ecab324
  正在确定要还原的项目…
  所有项目均是最新的，无法还原。
  Sdk.Analyzers -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\sdk\Sdk.Analyzers\bin\Debug\netstandard2.0\Microsoft.Azure.Functions.Worker.Sdk.Analyzers.dll
  TestUtility -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\test\TestUtility\bin\Debug\net7.0\Microsoft.Azure.Functions.Tests.TestUtility.dll
  FunctionMetadataLoaderExtension -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\sdk\FunctionMetadataLoaderExtension\bin\Debug\netstandard2.0\Microsoft.Azure.WebJobs.Exten 
  sions.FunctionMetadataLoader.dll
  Worker.Extensions.Abstractions -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\extensions\Worker.Extensions.Abstractions\src\bin\Debug\netstandard2.0\Microsoft.Azure.Func
  tions.Worker.Extensions.Abstractions.dll
  Sdk.Generators -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\sdk\Sdk.Generators\bin\Debug\netstandard2.0\Microsoft.Azure.Functions.Worker.Sdk.Generators.dll
  Worker.Extensions.Http.AspNetCore.Analyzers -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\extensions\Worker.Extensions.Http.AspNetCore.Analyzers\bin\Debug\netstandard2.
  0\Microsoft.Azure.Functions.Worker.Extensions.Http.AspNetCore.Analyzers.dll
  DependentAssemblyWithFunctions -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\test\DependentAssemblyWithFunctions\bin\Debug\net7.0\DependentAssemblyWithFunctions.dll     
  DotNetWorker.Core -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\src\DotNetWorker.Core\bin\Debug\netstandard2.0\Microsoft.Azure.Functions.Worker.Core.dll
  CustomMiddleware -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\samples\CustomMiddleware\bin\Debug\net8.0\CustomMiddleware.dll
  DependentAssemblyWithFunctions.NetStandard -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\test\DependentAssemblyWithFunctions.NetStandard\bin\Debug\netstandard2.0\Depend
  entAssemblyWithFunctions.NetStandard.dll
  Worker.Extensions.Warmup -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\extensions\Worker.Extensions.Warmup\src\bin\Debug\netstandard2.0\Microsoft.Azure.Functions.Worker 
  .Extensions.Warmup.dll
  Worker.Extensions.Timer -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\extensions\Worker.Extensions.Timer\src\bin\Debug\netstandard2.0\Microsoft.Azure.Functions.Worker.E 
  xtensions.Timer.dll
  Worker.Extensions.RabbitMQ -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\extensions\Worker.Extensions.RabbitMQ\src\bin\Debug\netstandard2.0\Microsoft.Azure.Functions.Wo
  rker.Extensions.RabbitMQ.dll
  Worker.Extensions.SendGrid -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\extensions\Worker.Extensions.SendGrid\src\bin\Debug\netstandard2.0\Microsoft.Azure.Functions.Wo 
  rker.Extensions.SendGrid.dll
  Worker.Extensions.SignalRService -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\extensions\Worker.Extensions.SignalRService\src\bin\Debug\netstandard2.0\Microsoft.Azure. 
  Functions.Worker.Extensions.SignalRService.dll
  Worker.Extensions.Shared.Tests -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\test\Worker.Extensions.Shared.Tests\bin\Debug\net7.0\Worker.Extensions.Shared.Tests.dll
  Worker.Extensions.Kafka -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\extensions\Worker.Extensions.Kafka\src\bin\Debug\netstandard2.0\Microsoft.Azure.Functions.Worker.E 
  xtensions.Kafka.dll
  正在确定要还原的项目…
  正在确定要还原的项目…
  DotNetWorker.Core -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\src\DotNetWorker.Core\bin\Debug\net5.0\Microsoft.Azure.Functions.Worker.Core.dll
  E2ETests -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\test\E2ETests\E2ETests\bin\Debug\net7.0\Microsoft.Azure.Functions.Worker.E2ETests.dll
  所有项目均是最新的，无法还原。
  正在确定要还原的项目…
  正在确定要还原的项目…
  正在确定要还原的项目…
  所有项目均是最新的，无法还原。
  Worker.Extensions.EventHubs -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\extensions\Worker.Extensions.EventHubs\src\bin\Debug\netstandard2.0\Microsoft.Azure.Functions.
  Worker.Extensions.EventHubs.dll
  Worker.Extensions.Tables -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\extensions\Worker.Extensions.Tables\src\bin\Debug\netstandard2.0\Microsoft.Azure.Functions.Worker
  .Extensions.Tables.dll
  Worker.Extensions.Http -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\extensions\Worker.Extensions.Http\src\bin\Debug\netstandard2.0\Microsoft.Azure.Functions.Worker.Ext 
  ensions.Http.dll
  Worker.Extensions.Storage.Queues -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\extensions\Worker.Extensions.Storage.Queues\src\bin\Debug\netstandard2.0\Microsoft.Azure. 
  Functions.Worker.Extensions.Storage.Queues.dll
  Worker.Extensions.Sample-IncorrectImplementation -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\test\Worker.Extensions.Sample-IncorrectImplementation\bin\Debug\netstanda
  rd2.0\Worker.Extensions.Sample-IncorrectImplementation.dll
  Worker.Extensions.Sample -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\test\Worker.Extensions.Sample\bin\Debug\netstandard2.0\Worker.Extensions.Sample.dll
  DotNetWorker.Grpc -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\src\DotNetWorker.Grpc\bin\Debug\net6.0\Microsoft.Azure.Functions.Worker.Grpc.dll
  Worker.Extensions.Rpc -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\extensions\Worker.Extensions.Rpc\src\bin\Debug\netstandard2.0\Microsoft.Azure.Functions.Worker.Exten
  sions.Rpc.dll
  WorkerExtensions -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\samples\NetFxWorker\obj\Debug\net48\WorkerExtensions\buildout\Microsoft.Azure.Functions.Worker.Extensions
  .dll
  Worker.Extensions.EventGrid -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\extensions\Worker.Extensions.EventGrid\src\bin\Debug\netstandard2.0\Microsoft.Azure.Functions. 
  Worker.Extensions.EventGrid.dll
  所有项目均是最新的，无法还原。
  Worker.Extensions.SignalRService.Tests -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\test\Worker.Extensions.SignalRService.Tests\bin\Debug\net7.0\Worker.Extensions.Sign
  alRService.Tests.dll
  Sdk -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\sdk\Sdk\bin\Debug\netstandard2.0\Microsoft.Azure.Functions.Worker.Sdk.dll
  WorkerExtensions -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\samples\Configuration\obj\Debug\net8.0\WorkerExtensions\buildout\Microsoft.Azure.Functions.Worker.Extensi 
  ons.dll
  Sdk -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\sdk\Sdk\bin\Debug\net472\Microsoft.Azure.Functions.Worker.Sdk.dll
  所有项目均是最新的，无法还原。
  正在确定要还原的项目…
  Worker.Extensions.Rpc -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\extensions\Worker.Extensions.Rpc\src\bin\Debug\net6.0\Microsoft.Azure.Functions.Worker.Extensions.Rp 
  c.dll
  所有项目均是最新的，无法还原。
  Worker.Extensions.CosmosDB -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\extensions\Worker.Extensions.CosmosDB\src\bin\Debug\netstandard2.0\Microsoft.Azure.Functions.Wo
  rker.Extensions.CosmosDB.dll
  DotNetWorker.Grpc -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\src\DotNetWorker.Grpc\bin\Debug\net5.0\Microsoft.Azure.Functions.Worker.Grpc.dll
  DotNetWorker -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\src\DotNetWorker\bin\Debug\net6.0\Microsoft.Azure.Functions.Worker.dll
  Worker.Extensions.ServiceBus -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\extensions\Worker.Extensions.ServiceBus\src\bin\Debug\netstandard2.0\Microsoft.Azure.Function
  s.Worker.Extensions.ServiceBus.dll
  DotNetWorker.ApplicationInsights -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\src\DotNetWorker.ApplicationInsights\bin\Debug\netstandard2.0\Microsoft.Azure.Functions.W 
  orker.ApplicationInsights.dll
  Worker.Extensions.Storage.Blobs -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\extensions\Worker.Extensions.Storage.Blobs\src\bin\Debug\netstandard2.0\Microsoft.Azure.Fu 
  nctions.Worker.Extensions.Storage.Blobs.dll
  DotNetWorker.Grpc -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\src\DotNetWorker.Grpc\bin\Debug\net7.0\Microsoft.Azure.Functions.Worker.Grpc.dll
  Worker.Extensions.Rpc.Tests -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\test\Worker.Extensions.Rpc.Tests\bin\Debug\net7.0\Worker.Extensions.Rpc.Tests.dll
  DotNetWorker -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\src\DotNetWorker\bin\Debug\net7.0\Microsoft.Azure.Functions.Worker.dll
  WorkerExtensions -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\samples\Extensions\obj\Debug\net8.0\WorkerExtensions\buildout\Microsoft.Azure.Functions.Worker.Extensions
  .dll
  SdkE2ETests -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\test\SdkE2ETests\bin\Debug\net7.0\Microsoft.Azure.Functions.SdkE2ETests.dll
  Worker.Extensions.Rpc.Tests -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\test\Worker.Extensions.Rpc.Tests\bin\Debug\net48\Worker.Extensions.Rpc.Tests.dll
  WorkerExtensions -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\samples\Net7Worker\obj\Debug\net7.0\WorkerExtensions\buildout\Microsoft.Azure.Functions.Worker.Extensions
  .dll
  Worker.Extensions.Storage -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\extensions\Worker.Extensions.Storage\src\bin\Debug\netstandard2.0\Microsoft.Azure.Functions.Work
  er.Extensions.Storage.dll
  E2EApp -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\test\E2ETests\E2EApps\E2EApp\bin\Debug\net8.0\Microsoft.Azure.Functions.Worker.E2EApp.dll
  Sdk.Analyzers.Tests -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\test\Sdk.Analyzers.Tests\bin\Debug\net7.0\Sdk.Analyzers.Tests.dll
  FunctionApp -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\samples\FunctionApp\bin\Debug\net8.0\FunctionApp.dll
  NetFxWorker -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\samples\NetFxWorker\bin\Debug\net48\NetFxWorker.exe
  正在确定要还原的项目…
  正在确定要还原的项目…
  DotNetWorker.Grpc -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\src\DotNetWorker.Grpc\bin\Debug\netstandard2.0\Microsoft.Azure.Functions.Worker.Grpc.dll
  Configuration -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\samples\Configuration\bin\Debug\net8.0\Configuration.dll
  DotNetWorker -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\src\DotNetWorker\bin\Debug\netstandard2.0\Microsoft.Azure.Functions.Worker.dll
  已还原 C:\Users\sky\AppData\Local\Temp\1tpad3lw.vns\WorkerExtensions.csproj (用时 550 ms)。
  WorkerExtensions -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\samples\EntityFramework\obj\Debug\net8.0\WorkerExtensions\buildout\Microsoft.Azure.Functions.Worker.Exten
  sions.dll
  Net7Worker -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\samples\Net7Worker\bin\Debug\net7.0\Net7Worker.dll
  Extensions -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\samples\Extensions\bin\Debug\net8.0\Extensions.dll
  已还原 C:\Users\sky\AppData\Local\Temp\vwl4yy2k.1ac\WorkerExtensions.csproj (用时 394 ms)。
  已还原 C:\Users\sky\AppData\Local\Temp\finw3nul.fi4\WorkerExtensions.csproj (用时 459 ms)。
  WorkerExtensions -> C:\Users\sky\AppData\Local\Temp\1tpad3lw.vns\buildout\Microsoft.Azure.Functions.Worker.Extensions.dll
  WorkerExtensions -> C:\Users\sky\AppData\Local\Temp\vwl4yy2k.1ac\buildout\Microsoft.Azure.Functions.Worker.Extensions.dll
  Worker.Extensions.Http.AspNetCore -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\extensions\Worker.Extensions.Http.AspNetCore\src\bin\Debug\net6.0\Microsoft.Azure.Functi
  ons.Worker.Extensions.Http.AspNetCore.dll
  EntityFramework -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\samples\EntityFramework\bin\Debug\net8.0\EntityFramework.dll
  WorkerExtensions -> C:\Users\sky\AppData\Local\Temp\finw3nul.fi4\buildout\Microsoft.Azure.Functions.Worker.Extensions.dll
  DotNetWorker -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\src\DotNetWorker\bin\Debug\net5.0\Microsoft.Azure.Functions.Worker.dll
  DotNetWorkerTests -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\test\DotNetWorkerTests\bin\Debug\net7.0\Microsoft.Azure.Functions.Worker.Tests.dll
  正在确定要还原的项目…
  SdkTests -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\test\FunctionMetadataGeneratorTests\bin\Debug\net7.0\Microsoft.Azure.Functions.SdkTests.dll
  所有项目均是最新的，无法还原。
  WorkerExtensions -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\samples\AspNetIntegration\obj\Debug\net8.0\WorkerExtensions\buildout\Microsoft.Azure.Functions.Worker.Ext
  ensions.dll
  Worker.Extensions.Tests -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\test\Worker.Extensions.Tests\bin\Debug\net7.0\Microsoft.Azure.Functions.Worker.Extensions.Tests.dl
  l
  Worker.Extensions.Http.AspNetCore.Tests -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\test\extensions\Worker.Extensions.Http.AspNetCore.Tests\bin\Debug\net7.0\Microsoft
  .Azure.Functions.Worker.Extensions.Http.AspNetCore.Tests.dll
  AspNetIntegration -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\samples\AspNetIntegration\bin\Debug\net8.0\AspNetIntegration.dll
  Sdk.Generator.Tests -> C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\test\Sdk.Generator.Tests\bin\Debug\net8.0\Microsoft.Azure.Functions.SdkGeneratorTests.dll

已成功生成。
    0 个警告
    0 个错误

已用时间 00:00:03.17
```


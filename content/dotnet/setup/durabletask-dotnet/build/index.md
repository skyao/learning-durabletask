---
title: "构建项目"
linkTitle: "build"
weight: 100
date: 2024-01-19
description: >
  构建 DurableTask-dotnet 项目
---

https://github.com/microsoft/durabletask-dotnet

##  获取源码

```bash
# cd ~/work/code/durabletask
git clone git@github.com:microsoft/durabletask-dotnet.git
git submodule update --init --recursive
# git submodule update --remote
```

当 submodule 有内容更新时，也就是说 durabletask-protobuf 项目有更新，则需要更新 submodule 的内容，需要执行：

```bash
git submodule update --remote
```

输出结果为：

```bash
remote: Enumerating objects: 7, done.
remote: Counting objects: 100% (7/7), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 4 (delta 2), reused 4 (delta 2), pack-reused 0
Unpacking objects: 100% (4/4), 431 bytes | 86.00 KiB/s, done.
From https://github.com/skyao/durabletask-protobuf  
   5f49779..6466c8c  versioning -> origin/versioning
Submodule path 'eng/proto': checked out '6466c8c4a35a4bcdd15dabcb499b4c71d83e8dd1'
```

这里 checked out 最新的代码更新。

## 执行 build

在终端执行命令：

```bash
# cd ~/work/code/durabletask/durabletask-dotnet
dotnet build
```

输出如下：

```bash
dotnet build
适用于 .NET MSBuild 版本 17.9.6+a4ecab324
  正在确定要还原的项目…
  所有项目均是最新的，无法还原。
  TestHelpers -> C:\Users\sky\work\code\durabletask\durabletask-dotnet\out\bin\Debug\TestHelpers\netstandard2.0\Microsoft.DurableTask.TestHelpers.dll
  Grpc -> C:\Users\sky\work\code\durabletask\durabletask-dotnet\out\bin\Debug\Grpc\netstandard2.0\Microsoft.DurableTask.Grpc.dll
  ConsoleApp -> C:\Users\sky\work\code\durabletask\durabletask-dotnet\out\samples\bin\Debug\ConsoleApp\net6.0\Samples.ConsoleApp.dll
  Generators -> C:\Users\sky\work\code\durabletask\durabletask-dotnet\out\bin\Debug\Generators\netstandard2.0\Microsoft.DurableTask.Generators.dll
  Abstractions -> C:\Users\sky\work\code\durabletask\durabletask-dotnet\out\bin\Debug\Abstractions\netstandard2.0\Microsoft.DurableTask.Abstractions.dll
  NetFxConsoleApp -> C:\Users\sky\work\code\durabletask\durabletask-dotnet\out\samples\bin\Debug\NetFxConsoleApp\net48\Samples.NetFxConsoleApp.exe
  Grpc -> C:\Users\sky\work\code\durabletask\durabletask-dotnet\out\bin\Debug\Grpc\net6.0\Microsoft.DurableTask.Grpc.dll
  AzureFunctionsApp -> C:\Users\sky\work\code\durabletask\durabletask-dotnet\out\samples\bin\Debug\AzureFunctionsApp\net6.0\Samples.AzureFunctionsApp.dll
  Worker -> C:\Users\sky\work\code\durabletask\durabletask-dotnet\out\bin\Debug\Worker\netstandard2.0\Microsoft.DurableTask.Worker.dll
  Client -> C:\Users\sky\work\code\durabletask\durabletask-dotnet\out\bin\Debug\Client\netstandard2.0\Microsoft.DurableTask.Client.dll
  Generators.Tests -> C:\Users\sky\work\code\durabletask\durabletask-dotnet\out\bin\Debug\Generators.Tests\net6.0\Microsoft.DurableTask.Generators.Tests.dll
  Abstractions.Tests -> C:\Users\sky\work\code\durabletask\durabletask-dotnet\out\bin\Debug\Abstractions.Tests\net6.0\Microsoft.DurableTask.Abstractions.Tests.dll
  Worker.Grpc -> C:\Users\sky\work\code\durabletask\durabletask-dotnet\out\bin\Debug\Worker.Grpc\netstandard2.0\Microsoft.DurableTask.Worker.Grpc.dll
  Worker.Grpc -> C:\Users\sky\work\code\durabletask\durabletask-dotnet\out\bin\Debug\Worker.Grpc\net6.0\Microsoft.DurableTask.Worker.Grpc.dll
  Benchmarks -> C:\Users\sky\work\code\durabletask\durabletask-dotnet\out\bin\Debug\Benchmarks\net6.0\Benchmarks.dll
  Client.Grpc -> C:\Users\sky\work\code\durabletask\durabletask-dotnet\out\bin\Debug\Client.Grpc\netstandard2.0\Microsoft.DurableTask.Client.Grpc.dll
  Client.Grpc -> C:\Users\sky\work\code\durabletask\durabletask-dotnet\out\bin\Debug\Client.Grpc\net6.0\Microsoft.DurableTask.Client.Grpc.dll
  Client.Tests -> C:\Users\sky\work\code\durabletask\durabletask-dotnet\out\bin\Debug\Client.Tests\net6.0\Microsoft.DurableTask.Client.Tests.dll
  Client.OrchestrationServiceClientShim -> C:\Users\sky\work\code\durabletask\durabletask-dotnet\out\bin\Debug\Client.OrchestrationServiceClientShim\netstandard2.0\Microsoft.DurableTask.Client.OrchestrationServiceClientShim.dll
  Worker.Tests -> C:\Users\sky\work\code\durabletask\durabletask-dotnet\out\bin\Debug\Worker.Tests\net6.0\Microsoft.DurableTask.Worker.Tests.dll
  Client.OrchestrationServiceClientShim.Tests -> C:\Users\sky\work\code\durabletask\durabletask-dotnet\out\bin\Debug\Client.OrchestrationServiceClientShim.Tests\net6.0\Microsoft.DurableTask.Client.OrchestrationServiceClientShim.Tests.dll
  正在确定要还原的项目…
  Grpc.IntegrationTests -> C:\Users\sky\work\code\durabletask\durabletask-dotnet\out\bin\Debug\Grpc.IntegrationTests\net6.0\Microsoft.DurableTask.Grpc.IntegrationTests.dll
  Client.Grpc.Tests -> C:\Users\sky\work\code\durabletask\durabletask-dotnet\out\bin\Debug\Client.Grpc.Tests\net6.0\Microsoft.DurableTask.Client.Grpc.Tests.dll
  Worker.Grpc.Tests -> C:\Users\sky\work\code\durabletask\durabletask-dotnet\out\bin\Debug\Worker.Grpc.Tests\net6.0\Microsoft.DurableTask.Worker.Grpc.Tests.dll
  WebAPI -> C:\Users\sky\work\code\durabletask\durabletask-dotnet\out\samples\bin\Debug\WebAPI\net6.0\Samples.WebAPI.dll
  已还原 C:\Users\sky\AppData\Local\Temp\n4zwr33f.gin\WorkerExtensions.csproj (用时 300 ms)。
  WorkerExtensions -> C:\Users\sky\AppData\Local\Temp\n4zwr33f.gin\buildout\Microsoft.Azure.Functions.Worker.Extensions.dll

已成功生成。
    0 个警告
    0 个错误

已用时间 00:00:02.01
```


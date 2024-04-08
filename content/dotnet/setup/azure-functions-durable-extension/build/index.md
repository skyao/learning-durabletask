---
title: "构建项目"
linkTitle: "build"
weight: 100
date: 2024-01-19
description: >
  构建 azure-functions-durable-extension 项目
---

https://github.com/Azure/azure-functions-durable-extension

##  获取源码

```bash
# cd ~/work/code/durabletask
git clone git@github.com:Azure/azure-functions-durable-extension.git
```

## 执行 build

注意：azure-functions-durable-extension 项目需要 .net 2.1 

```bash
C:\Users\sky\.nuget\packages\microsoft.net.sdk.functions\1.0.30\build\Microsoft.NET.Sdk.Functions.Build.targets(41,5): error : To install missing framework, download: [C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\test\DFPerfScenariosV1\DFPerfScenariosV1.csproj]
C:\Users\sky\.nuget\packages\microsoft.net.sdk.functions\1.0.30\build\Microsoft.NET.Sdk.Functions.Build.targets(41,5): error : https://aka.ms/dotnet-core-applaunch?framework=Microsoft.NETCore.App&framework_version=2.1.0&arch=x64&rid=win-x64&os=win10 [C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\test\DFPerfScenariosV1\DFPerfScenariosV1.csproj]
```



在终端执行命令：

```bash
# cd ~/work/code/durabletask/azure-functions-durable-extension
dotnet build
```

输出如下：

```bash
dotnet build
适用于 .NET MSBuild 版本 17.9.6+a4ecab324
  正在确定要还原的项目…
C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\test\SmokeTests\SmokeTestsV1\VSSampleV1.csproj : warning NU1903: Package 'Newtonsoft.Json' 9.0.1 has a known high severity vulnerability, https://github.com/advisories/GHSA-5crp-9r3c-p9vr [C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\WebJobs.Extensions.DurableTask.sln]
C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\src\WebJobs.Extensions.DurableTask\WebJobs.Extensions.DurableTask.csproj : warning NU1903: Package 'Azure.Identity' 1.1.1 has a known high severity vulnerability, https://github.com/advisories/GHSA-5mfx-4wcx-rv27 [C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\WebJobs.Extensions.DurableTask.sln]
  所有项目均是最新的，无法还原。
C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\test\SmokeTests\SmokeTestsV1\VSSampleV1.csproj : warning NU1903: Package 'Newtonsoft.Json' 9.0.1 has a known high severity vulnerability, https://github.com/advisories/GHSA-5crp-9r3c-p9vr
C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\src\WebJobs.Extensions.DurableTask\WebJobs.Extensions.DurableTask.csproj : warning NU1903: Package 'Azure.Identity' 1.1.1 has a known high severity vulnerability, https://github.com/advisories/GHSA-5mfx-4wcx-rv27 [TargetFramework=netcoreapp3.1]
C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\src\WebJobs.Extensions.DurableTask\WebJobs.Extensions.DurableTask.csproj : warning NU1903: Package 'Azure.Identity' 1.1.1 has a known high severity vulnerability, https://github.com/advisories/GHSA-5mfx-4wcx-rv27 [TargetFramework=netstandard2.0]
  WebJobs.Extensions.DurableTask.Analyzers -> C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\src\WebJobs.Extensions.DurableTask.Analyzers\bin\Debug\netstandard2.0\Microsoft.Azure.WebJobs.Extensions.DurableTask.Analyzers.dll
  DurableFunctions.TypedInterfaces -> C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\src\DurableFunctions.TypedInterfaces\SourceGenerator\bin\Debug\netstandard2.0\DurableFunctions.TypedInterfaces.dll
  Worker.Extensions.DurableTask -> C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\src\Worker.Extensions.DurableTask\bin\Debug\netstandard2.0\Microsoft.Azure.Functions.Worker.Extensions.DurableTask.dll
C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\src\WebJobs.Extensions.DurableTask\WebJobs.Extensions.DurableTask.csproj : warning NU1903: Package 'Azure.Identity' 1.1.1 has a known high severity vulnerability, https://github.com/advisories/GHSA-5mfx-4wcx-rv27 [TargetFramework=net462]
  Worker.Extensions.DurableTask -> C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\src\Worker.Extensions.DurableTask\bin\Debug\net6.0\Microsoft.Azure.Functions.Worker.Extensions.DurableTask.dll
  WebJobs.Extensions.DurableTask -> C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\src\WebJobs.Extensions.DurableTask\bin\Debug\netstandard2.0\Microsoft.Azure.WebJobs.Extensions.DurableTask.dll
  WebJobs.Extensions.DurableTask -> C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\src\WebJobs.Extensions.DurableTask\bin\Debug\netcoreapp3.1\Microsoft.Azure.WebJobs.Extensions.DurableTask.dll
  WebJobs.Extensions.DurableTask -> C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\src\WebJobs.Extensions.DurableTask\bin\Debug\net462\Microsoft.Azure.WebJobs.Extensions.DurableTask.dll
  WebJobs.Extensions.DurableTask.Analyzers.Test -> C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\test\WebJobs.Extensions.DurableTask.Analyzers.Test\bin\Debug\net6.0\Microsoft.Azure.WebJobs.Extensions.DurableTask.Analyzers.Test.dll
  extensions -> C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\test\TimeoutTests\Python\bin\Debug\netstandard2.0\extensions.dll
  DFPerfScenariosV4 -> C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\test\DFPerfScenarios\bin\Debug\net60\DFPerfScenariosV4.dll
  VSSampleV3 -> C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\test\SmokeTests\SmokeTestsV3\bin\Debug\netcoreapp3.1\VSSampleV3.dll
  WebJobs.Extensions.DurableTask.Tests.V2 -> C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\test\FunctionsV2\bin\Debug\net6.0\WebJobs.Extensions.DurableTask.Tests.V2.dll
  VSSampleV1 -> C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\test\SmokeTests\SmokeTestsV1\bin\Debug\net462\bin\VSSampleV1.dll
  DFPerfScenariosV1 -> C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\test\DFPerfScenariosV1\bin\Debug\net462\bin\DFPerfScenariosV1.dll
  WebJobs.Extensions.DurableTask.Tests.V1 -> C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\test\FunctionsV1\bin\Debug\net462\WebJobs.Extensions.DurableTask.Tests.V1.dll
  DurableFunctions.TypedInterfaces.SourceGenerator.Test -> C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\test\CodeGen.SourceGenerator.Test\bin\Debug\net6.0\DurableFunctions.TypedInterfaces.SourceGenerator.Test.dll
  DurableFunctions.TypedInterfaces.Example -> C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\src\DurableFunctions.TypedInterfaces\Example\bin\Debug\net6.0\DurableFunctions.TypedInterfaces.Example.dll
  VSSampleV2 -> C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\test\SmokeTests\SmokeTestsV2\bin\Debug\netstandard2.0\bin\VSSampleV2.dll
  TimeoutTests -> C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\test\TimeoutTests\CSharp\bin\Debug\netstandard2.0\TimeoutTests.dll
  extensions -> C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\test\SmokeTests\OOProcSmokeTests\durableJS\bin\Debug\netstandard2.0\extensions.dll
  extensions -> C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\test\SmokeTests\OOProcSmokeTests\durablePy\bin\Debug\netstandard2.0\extensions.dll
C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\src\WebJobs.Extensions.DurableTask\WebJobs.Extensions.DurableTask.csproj : warning NU1903: Package 'Azure.Identity' 1.1.1 has a known high severity vulnerability, https://github.com/advisories/GHSA-5mfx-4wcx-rv27 [TargetFramework=net462]
C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\src\WebJobs.Extensions.DurableTask\WebJobs.Extensions.DurableTask.csproj : warning NU1903: Package 'Azure.Identity' 1.1.1 has a known high severity vulnerability, https://github.com/advisories/GHSA-5mfx-4wcx-rv27 [TargetFramework=netcoreapp3.1]
C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\src\WebJobs.Extensions.DurableTask\WebJobs.Extensions.DurableTask.csproj : warning NU1903: Package 'Azure.Identity' 1.1.1 has a known high severity vulnerability, https://github.com/advisories/GHSA-5mfx-4wcx-rv27 [TargetFramework=netstandard2.0]
  DurableFunctions.TypedInterfaces.Examples.Test -> C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\test\CodeGen.Example.Test\bin\Debug\net6.0\DurableFunctions.TypedInterfaces.Examples.Test.dll

已成功生成。

C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\test\SmokeTests\SmokeTestsV1\VSSampleV1.csproj : warning NU1903: Package 'Newtonsoft.Json' 9.0.1 has a known high severity vulnerability, https://github.com/advisories/GHSA-5crp-9r3c-p9vr [C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\WebJobs.Extensions.DurableTask.sln]
C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\src\WebJobs.Extensions.DurableTask\WebJobs.Extensions.DurableTask.csproj : warning NU1903: Package 'Azure.Identity' 1.1.1 has a known high severity vulnerability, https://github.com/advisories/GHSA-5mfx-4wcx-rv27 [C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\WebJobs.Extensions.DurableTask.sln]
C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\test\SmokeTests\SmokeTestsV1\VSSampleV1.csproj : warning NU1903: Package 'Newtonsoft.Json' 9.0.1 has a known high severity vulnerability, https://github.com/advisories/GHSA-5crp-9r3c-p9vr
C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\src\WebJobs.Extensions.DurableTask\WebJobs.Extensions.DurableTask.csproj : warning NU1903: Package 'Azure.Identity' 1.1.1 has a known high severity vulnerability, https://github.com/advisories/GHSA-5mfx-4wcx-rv27 [TargetFramework=netcoreapp3.1]
C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\src\WebJobs.Extensions.DurableTask\WebJobs.Extensions.DurableTask.csproj : warning NU1903: Package 'Azure.Identity' 1.1.1 has a known high severity vulnerability, https://github.com/advisories/GHSA-5mfx-4wcx-rv27 [TargetFramework=netstandard2.0]
C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\src\WebJobs.Extensions.DurableTask\WebJobs.Extensions.DurableTask.csproj : warning NU1903: Package 'Azure.Identity' 1.1.1 has a known high severity vulnerability, https://github.com/advisories/GHSA-5mfx-4wcx-rv27 [TargetFramework=net462]
C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\src\WebJobs.Extensions.DurableTask\WebJobs.Extensions.DurableTask.csproj : warning NU1903: Package 'Azure.Identity' 1.1.1 has a known high severity vulnerability, https://github.com/advisories/GHSA-5mfx-4wcx-rv27 [TargetFramework=net462]
C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\src\WebJobs.Extensions.DurableTask\WebJobs.Extensions.DurableTask.csproj : warning NU1903: Package 'Azure.Identity' 1.1.1 has a known high severity vulnerability, https://github.com/advisories/GHSA-5mfx-4wcx-rv27 [TargetFramework=netcoreapp3.1]
C:\Users\sky\work\code\durabletask\azure-functions-durable-extension\src\WebJobs.Extensions.DurableTask\WebJobs.Extensions.DurableTask.csproj : warning NU1903: Package 'Azure.Identity' 1.1.1 has a known high severity vulnerability, https://github.com/advisories/GHSA-5mfx-4wcx-rv27 [TargetFramework=netstandard2.0]
    9 个警告
    0 个错误

已用时间 00:00:02.03
```


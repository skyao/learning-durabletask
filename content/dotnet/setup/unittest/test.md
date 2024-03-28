---
title: "运行单元测试"
linkTitle: "运行"
weight: 30
date: 2024-01-19
description: >
  运行 Durable Task 单元测试
---



### DurableTask.Core.Tests

路径为 `Test/DurableTask.Core.Tests`，不需要启动模拟器或者设置 service bus 连接，直接运行：

```bash
dotnet test Test/DurableTask.Core.Tests/DurableTask.Core.Tests.csproj
```

输出为:

```bash
  Determining projects to restore...
  All projects are up-to-date for restore.
  DurableTask.Core -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.Core\bin\Debug\netstandard2.0\DurableTask.Core.dll
  DurableTask.Core -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.Core\bin\Debug\net462\DurableTask.Core.dll
  DurableTask.Emulator -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.Emulator\bin\Debug\netstandard2.0\DurableTask.Emulator.dll
  DurableTask.Test.Orchestrations -> C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Test.Orchestrations\bin\Debug\netstandard2.0\DurableTask.Test.Orchestrati
  ons.dll
  DurableTask.Emulator -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.Emulator\bin\Debug\net462\DurableTask.Emulator.dll
  DurableTask.Test.Orchestrations -> C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Test.Orchestrations\bin\Debug\net462\DurableTask.Test.Orchestrations.dll
  DurableTask.Core.Tests -> C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Core.Tests\bin\Debug\netcoreapp3.1\DurableTask.Core.Tests.dll
  DurableTask.Core.Tests -> C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Core.Tests\bin\Debug\net462\DurableTask.Core.Tests.dll
Test run for C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Core.Tests\bin\Debug\netcoreapp3.1\DurableTask.Core.Tests.dll (.NETCoreApp,Version=v3.1)
Microsoft (R) Test Execution Command Line Tool Version 17.9.0 (x64)
Copyright (c) Microsoft Corporation.  All rights reserved.

Starting test execution, please wait...
A total of 1 test files matched the specified pattern.

Passed!  - Failed:     0, Passed:    59, Skipped:     0, Total:    59, Duration: 21 s - DurableTask.Core.Tests.dll (netcoreapp3.1)
Test run for C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Core.Tests\bin\Debug\net462\DurableTask.Core.Tests.dll (.NETFramework,Version=v4.6.2)
Microsoft (R) Test Execution Command Line Tool Version 17.9.0 (x64)
Copyright (c) Microsoft Corporation.  All rights reserved.

Starting test execution, please wait...
A total of 1 test files matched the specified pattern.

Passed!  - Failed:     0, Passed:    59, Skipped:     0, Total:    59, Duration: 21 s - DurableTask.Core.Tests.dll (net462)

```



### DurableTask.Redis.Tests

```bash
dotnet test Test/DurableTask.Redis.Tests/DurableTask.Redis.Tests.csproj
```

报错无法连接 redis 服务器：

```bash
dotnet test Test/DurableTask.Redis.Tests/DurableTask.Redis.Tests.csproj
  Determining projects to restore...
  Restored C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Redis.Tests\DurableTask.Redis.Tests.csproj (in 329 ms).
  2 of 3 projects are up-to-date for restore.
  DurableTask.Core -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.Core\bin\Debug\netstandard2.0\DurableTask.Core.dll
  DurableTask.Redis -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.Redis\bin\Debug\netstandard2.0\DurableTask.Redis.dll
  The package Microsoft.Azure.DurableTask.Redis.0.1.9-alpha is missing a readme. Go to https://aka.ms/nuget/authoring-best-practices/readme to learn why package readmes are i
  mportant.
  Successfully created package 'C:\Users\sky\work\code\durabletask\durabletask\build_output\packages\Microsoft.Azure.DurableTask.Redis.0.1.9-alpha.nupkg'.
  DurableTask.Redis.Tests -> C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Redis.Tests\bin\Debug\netcoreapp3.1\DurableTask.Redis.Tests.dll
Test run for C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Redis.Tests\bin\Debug\netcoreapp3.1\DurableTask.Redis.Tests.dll (.NETCoreApp,Version=v3.1)
Microsoft (R) Test Execution Command Line Tool Version 17.9.0 (x64)
Copyright (c) Microsoft Corporation.  All rights reserved.

Starting test execution, please wait...
A total of 1 test files matched the specified pattern.
[xUnit.net 00:00:13.73]     DurableTask.Redis.Tests.EndToEndServiceScenarioTests.SimpleFanOutOrchestration [FAIL]
  Failed DurableTask.Redis.Tests.EndToEndServiceScenarioTests.SimpleFanOutOrchestration [13 s]
  Error Message:
   StackExchange.Redis.RedisConnectionException : It was not possible to connect to the redis server(s). UnableToConnect on localhost:6379/Interactive, Initializing, last: NONE, origin: BeginConnectAsync, outstanding: 0, last-read: 2s ago, last-write: 2s ago, keep-alive: 60s, state: Connecting, mgr: 10 of 10 available, last-heartbeat: never, global: 11s ago, v: 2.0.571.20511
  Stack Trace:
     at StackExchange.Redis.ConnectionMultiplexer.ConnectImplAsync(Object configuration, TextWriter log) in C:\projects\stackexchange-redis\src\StackExchange.Redis\ConnectionMultiplexer.cs:line 825
   at DurableTask.Redis.RedisOrchestrationService.DeleteAsync() in C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.Redis\RedisOrchestrationService.cs:line 267  
   at DurableTask.Redis.Tests.EndToEndServiceScenarioTests.SimpleFanOutOrchestration() in C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Redis.Tests\EndToEndServiceScenarioTests.cs:line 148
--- End of stack trace from previous location where exception was thrown ---
[xUnit.net 00:00:21.90]     DurableTask.Redis.Tests.EndToEndServiceScenarioTests.ScheduledStart_NotSupported [FAIL]
  Failed DurableTask.Redis.Tests.EndToEndServiceScenarioTests.ScheduledStart_NotSupported [8 s]
  Error Message:
   StackExchange.Redis.RedisConnectionException : It was not possible to connect to the redis server(s). UnableToConnect on localhost:6379/Interactive, Initializing, last: NONE, origin: BeginConnectAsync, outstanding: 0, last-read: 2s ago, last-write: 2s ago, keep-alive: 60s, state: Connecting, mgr: 10 of 10 available, last-heartbeat: never, global: 19s ago, v: 2.0.571.20511
  Stack Trace:
     at StackExchange.Redis.ConnectionMultiplexer.ConnectImplAsync(Object configuration, TextWriter log) in C:\projects\stackexchange-redis\src\StackExchange.Redis\ConnectionMultiplexer.cs:line 825
   at DurableTask.Redis.RedisOrchestrationService.DeleteAsync() in C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.Redis\RedisOrchestrationService.cs:line 267  
   at DurableTask.Redis.Tests.EndToEndServiceScenarioTests.ScheduledStart_NotSupported() in C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Redis.Tests\EndToEndServiceScenarioTests.cs:line 229
--- End of stack trace from previous location where exception was thrown ---
[xUnit.net 00:00:30.14]     DurableTask.Redis.Tests.EndToEndServiceScenarioTests.SimpleFanOutOrchestration_DurabilityTest [FAIL]
  Failed DurableTask.Redis.Tests.EndToEndServiceScenarioTests.SimpleFanOutOrchestration_DurabilityTest [8 s]
  Error Message:
   StackExchange.Redis.RedisConnectionException : It was not possible to connect to the redis server(s). UnableToConnect on localhost:6379/Interactive, Initializing, last: NONE, origin: BeginConnectAsync, outstanding: 0, last-read: 2s ago, last-write: 2s ago, keep-alive: 60s, state: Connecting, mgr: 10 of 10 available, last-heartbeat: never, global: 27s ago, v: 2.0.571.20511
  Stack Trace:
     at StackExchange.Redis.ConnectionMultiplexer.ConnectImplAsync(Object configuration, TextWriter log) in C:\projects\stackexchange-redis\src\StackExchange.Redis\ConnectionMultiplexer.cs:line 825
   at DurableTask.Redis.RedisOrchestrationService.DeleteAsync() in C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.Redis\RedisOrchestrationService.cs:line 267  
   at DurableTask.Redis.Tests.EndToEndServiceScenarioTests.SimpleFanOutOrchestration_DurabilityTest() in C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Redis.Tests\EndToEndServiceScenarioTests.cs:line 202
--- End of stack trace from previous location where exception was thrown ---
[xUnit.net 00:00:34.23]     DurableTask.Redis.Tests.EndToEndServiceScenarioTests.DeleteTaskHub_DeletesAllKeysInRelevantNamespace [FAIL]
  Failed DurableTask.Redis.Tests.EndToEndServiceScenarioTests.DeleteTaskHub_DeletesAllKeysInRelevantNamespace [4 s]
  Error Message:
   StackExchange.Redis.RedisConnectionException : It was not possible to connect to the redis server(s). UnableToConnect on localhost:6379/Interactive, Initializing, last: NONE, origin: BeginConnectAsync, outstanding: 0, last-read: 2s ago, last-write: 2s ago, keep-alive: 60s, state: Connecting, mgr: 10 of 10 available, last-heartbeat: never, global: 31s ago, v: 2.0.571.20511
  Stack Trace:
     at StackExchange.Redis.ConnectionMultiplexer.ConnectImplAsync(Object configuration, TextWriter log) in C:\projects\stackexchange-redis\src\StackExchange.Redis\ConnectionMultiplexer.cs:line 825
   at DurableTask.Redis.Tests.TestHelpers.GetRedisConnection() in C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Redis.Tests\TestHelpers.cs:line 39
   at DurableTask.Redis.Tests.EndToEndServiceScenarioTests.DeleteTaskHub_DeletesAllKeysInRelevantNamespace() in C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Redis.Tests\EndToEndServiceScenarioTests.cs:line 31
--- End of stack trace from previous location where exception was thrown ---
[xUnit.net 00:00:42.37]     DurableTask.Redis.Tests.EndToEndServiceScenarioTests.SimpleGreetingOrchestration [FAIL]
  Failed DurableTask.Redis.Tests.EndToEndServiceScenarioTests.SimpleGreetingOrchestration [8 s]
  Error Message:
   StackExchange.Redis.RedisConnectionException : It was not possible to connect to the redis server(s). UnableToConnect on localhost:6379/Interactive, Initializing, last: NONE, origin: BeginConnectAsync, outstanding: 0, last-read: 2s ago, last-write: 2s ago, keep-alive: 60s, state: Connecting, mgr: 10 of 10 available, last-heartbeat: never, global: 39s ago, v: 2.0.571.20511
  Stack Trace:
     at StackExchange.Redis.ConnectionMultiplexer.ConnectImplAsync(Object configuration, TextWriter log) in C:\projects\stackexchange-redis\src\StackExchange.Redis\ConnectionMultiplexer.cs:line 825
   at DurableTask.Redis.RedisOrchestrationService.DeleteAsync() in C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.Redis\RedisOrchestrationService.cs:line 267  
   at DurableTask.Redis.Tests.EndToEndServiceScenarioTests.SimpleGreetingOrchestration() in C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Redis.Tests\EndToEndServiceScenarioTests.cs:line 110
--- End of stack trace from previous location where exception was thrown ---

Failed!  - Failed:     5, Passed:     6, Skipped:     0, Total:    11, Duration: 41 s - DurableTask.Redis.Tests.dll (netcoreapp3.1)
```



### DurableTask.Emulator.Tests

不需要启动模拟器或者设置 service bus 连接，直接运行：

```bash
dotnet test Test/DurableTask.Emulator.Tests/DurableTask.Emulator.Tests.csproj
```

输出为：

```bash
dotnet test Test/DurableTask.Emulator.Tests/DurableTask.Emulator.Tests.csproj 
  Determining projects to restore...
  Restored C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Test.Orchestrations\DurableTask.Test.Orchestrations.csproj (in 192 ms).
  Restored C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Emulator.Tests\DurableTask.Emulator.Tests.csproj (in 192 ms).
  2 of 4 projects are up-to-date for restore.
  DurableTask.Core -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.Core\bin\Debug\netstandard2.0\DurableTask.Core.dll
  DurableTask.Core -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.Core\bin\Debug\net462\DurableTask.Core.dll
  DurableTask.Emulator -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.Emulator\bin\Debug\net462\DurableTask.Emulator.dll
  DurableTask.Test.Orchestrations -> C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Test.Orchestrations\bin\Debug\net462\DurableTask.Test.Orchestrations.dll
  DurableTask.Emulator -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.Emulator\bin\Debug\netstandard2.0\DurableTask.Emulator.dll
  DurableTask.Emulator.Tests -> C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Emulator.Tests\bin\Debug\net462\DurableTask.Emulator.Tests.dll
  DurableTask.Test.Orchestrations -> C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Test.Orchestrations\bin\Debug\netstandard2.0\DurableTask.Test.Orchestrati
  ons.dll
  DurableTask.Emulator.Tests -> C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Emulator.Tests\bin\Debug\netcoreapp3.1\DurableTask.Emulator.Tests.dll
Test run for C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Emulator.Tests\bin\Debug\net462\DurableTask.Emulator.Tests.dll (.NETFramework,Version=v4.6.2)
Microsoft (R) Test Execution Command Line Tool Version 17.9.0 (x64)
Copyright (c) Microsoft Corporation.  All rights reserved.

Starting test execution, please wait...
A total of 1 test files matched the specified pattern.

Passed!  - Failed:     0, Passed:     5, Skipped:     0, Total:     5, Duration: 34 s - DurableTask.Emulator.Tests.dll (net462)
Test run for C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Emulator.Tests\bin\Debug\netcoreapp3.1\DurableTask.Emulator.Tests.dll (.NETCoreApp,Version=v3.1)
Microsoft (R) Test Execution Command Line Tool Version 17.9.0 (x64)
Copyright (c) Microsoft Corporation.  All rights reserved.

Starting test execution, please wait...
A total of 1 test files matched the specified pattern.

Passed!  - Failed:     0, Passed:     5, Skipped:     0, Total:     5, Duration: 32 s - DurableTask.Emulator.Tests.dll (netcoreapp3.1)
```



### DurableTask.Samples.Tests



```bash
dotnet test Test/DurableTask.Samples.Tests/DurableTask.Samples.Tests.csproj 
  Determining projects to restore...
C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Samples.Tests\DurableTask.Samples.Tests.csproj : error NU1201: Project DurableTask.Samples is not compatible w
ith net451 (.NETFramework,Version=v4.5.1). Project DurableTask.Samples supports: net462 (.NETFramework,Version=v4.6.2)
  Failed to restore C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Samples.Tests\DurableTask.Samples.Tests.csproj (in 4.15 sec).
  3 of 4 projects are up-to-date for restore.
```



### DurableTask.ServiceBus.Tests



```bash
dotnet test Test/DurableTask.ServiceBus.Tests/DurableTask.ServiceBus.Tests.csproj
```

必须设置 service bus 连接，否则报错：

```bash
  Failed GenerationSubNoCompressionTest [< 1 ms]
  Error Message:
   Initialization method DurableTask.ServiceBus.Tests.FunctionalTests.TestInitialize threw exception. System.TypeInitializationException: The type initializer for 'DurableTask.ServiceBus.Tests.TestHelpers' threw an exception. ---> System.ArgumentException: A ServiceBus connection string must be defined in either an environment variable or in configuration..
```



### DurableTask.SqlServer.Tests

```bash
dotnet test Test/DurableTask.SqlServer.Tests/DurableTask.SqlServer.Tests.csproj
```





```bash
dotnet test Test/DurableTask.SqlServer.Tests/DurableTask.SqlServer.Tests.csproj
  Determining projects to restore...
C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.SqlServer.Tests\DurableTask.SqlServer.Tests.csproj : error NU1903: Warning As Error: Package 'System.Data.SqlC
lient' 4.8.5 has a known high severity vulnerability, https://github.com/advisories/GHSA-98g6-xh36-x2p7
  Failed to restore C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.SqlServer.Tests\DurableTask.SqlServer.Tests.csproj (in 280 ms).
  2 of 3 projects are up-to-date for restore.
```



### DurableTask.Stress.Tests

```bash
dotnet test Test/DurableTask.Stress.Tests/DurableTask.Stress.Tests.csproj 
```

没有测试类：

```bash
dotnet test Test/DurableTask.Stress.Tests/DurableTask.Stress.Tests.csproj 
  Determining projects to restore...
C:\Program Files\dotnet\sdk\8.0.203\Sdks\Microsoft.NET.Sdk\targets\Microsoft.NET.EolTargetFrameworks.targets(32,5): warning NETSDK1138: The target framework 'netcoreapp3.1' i
s out of support and will not receive security updates in the future. Please refer to https://aka.ms/dotnet-core-support for more information about the support policy. [C:\Us
ers\sky\work\code\durabletask\durabletask\Test\DurableTask.Stress.Tests\DurableTask.Stress.Tests.csproj::TargetFramework=netcoreapp3.1]
  Restored C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Stress.Tests\DurableTask.Stress.Tests.csproj (in 521 ms).
  3 of 4 projects are up-to-date for restore.
C:\Program Files\dotnet\sdk\8.0.203\Sdks\Microsoft.NET.Sdk\targets\Microsoft.NET.EolTargetFrameworks.targets(32,5): warning NETSDK1138: The target framework 'netcoreapp3.1' i
s out of support and will not receive security updates in the future. Please refer to https://aka.ms/dotnet-core-support for more information about the support policy. [C:\Us 
ers\sky\work\code\durabletask\durabletask\Test\DurableTask.Stress.Tests\DurableTask.Stress.Tests.csproj::TargetFramework=netcoreapp3.1]
  DurableTask.Core -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.Core\bin\Debug\netstandard2.0\DurableTask.Core.dll
  DurableTask.Core -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.Core\bin\Debug\net462\DurableTask.Core.dll
  DurableTask.Test.Orchestrations -> C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Test.Orchestrations\bin\Debug\netstandard2.0\DurableTask.Test.Orchestrati
  ons.dll
  DurableTask.AzureStorage -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.AzureStorage\bin\Debug\netstandard2.0\DurableTask.AzureStorage.dll
  DurableTask.Test.Orchestrations -> C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Test.Orchestrations\bin\Debug\net462\DurableTask.Test.Orchestrations.dll
  DurableTask.AzureStorage -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.AzureStorage\bin\Debug\net462\DurableTask.AzureStorage.dll
  DurableTask.Stress.Tests -> C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Stress.Tests\bin\Debug\net462\DurableTask.Stress.Tests.exe
  DurableTask.Stress.Tests -> C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Stress.Tests\bin\Debug\netcoreapp3.1\DurableTask.Stress.Tests.dll
```



### DurableTask.Test.Orchestrations



```bash
dotnet test Test/DurableTask.Test.Orchestrations/DurableTask.Test.Orchestrations.csproj 
  Determining projects to restore...
  All projects are up-to-date for restore.
  DurableTask.Core -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.Core\bin\Debug\netstandard2.0\DurableTask.Core.dll
  DurableTask.Core -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.Core\bin\Debug\net462\DurableTask.Core.dll
  DurableTask.Test.Orchestrations -> C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Test.Orchestrations\bin\Debug\netstandard2.0\DurableTask.Test.Orchestrati
  ons.dll
  DurableTask.Test.Orchestrations -> C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Test.Orchestrations\bin\Debug\net462\DurableTask.Test.Orchestrations.dll
```


---
title: "构建项目"
linkTitle: "build"
weight: 100
date: 2024-01-19
description: >
  构建 Durable Task 项目
---



## 执行 build

### 遇到问题

在终端执行命令：

```bash
dotnet build
```

报错如下：

```bash
dotnet build
MSBuild version 17.9.6+a4ecab324 for .NET
  Determining projects to restore...
C:\Program Files\dotnet\sdk\8.0.203\Sdks\Microsoft.NET.Sdk\targets\Microsoft.NET.EolTargetFrameworks.targets(32,5): warning NETSDK1138: The target framework 'netcoreapp3.1' is 
out of support and will not receive security updates in the future. Please refer to https://aka.ms/dotnet-core-support for more information about the support policy. [C:\Users\
sky\work\code\durabletask\durabletask\samples\Correlation.Samples\Correlation.Samples.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Sdks\Microsoft.NET.Sdk\targets\Microsoft.NET.EolTargetFrameworks.targets(32,5): warning NETSDK1138: The target framework 'netcoreapp3.1' is 
out of support and will not receive security updates in the future. Please refer to https://aka.ms/dotnet-core-support for more information about the support policy. [C:\Users\
sky\work\code\durabletask\durabletask\test\DurableTask.Stress.Tests\DurableTask.Stress.Tests.csproj::TargetFramework=netcoreapp3.1]
C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.SqlServer.Tests\DurableTask.SqlServer.Tests.csproj : error NU1903: Warning As Error: Package 'System.Data.SqlCli
ent' 4.8.5 has a known high severity vulnerability, https://github.com/advisories/GHSA-98g6-xh36-x2p7 [C:\Users\sky\work\code\durabletask\durabletask\DurableTask.sln]
  Failed to restore C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.SqlServer.Tests\DurableTask.SqlServer.Tests.csproj (in 374 ms).
  23 of 24 projects are up-to-date for restore.

Build FAILED.

C:\Program Files\dotnet\sdk\8.0.203\Sdks\Microsoft.NET.Sdk\targets\Microsoft.NET.EolTargetFrameworks.targets(32,5): warning NETSDK1138: The target framework 'netcoreapp3.1' is 
out of support and will not receive security updates in the future. Please refer to https://aka.ms/dotnet-core-support for more information about the support policy. [C:\Users\
sky\work\code\durabletask\durabletask\samples\Correlation.Samples\Correlation.Samples.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Sdks\Microsoft.NET.Sdk\targets\Microsoft.NET.EolTargetFrameworks.targets(32,5): warning NETSDK1138: The target framework 'netcoreapp3.1' is 
out of support and will not receive security updates in the future. Please refer to https://aka.ms/dotnet-core-support for more information about the support policy. [C:\Users\
sky\work\code\durabletask\durabletask\test\DurableTask.Stress.Tests\DurableTask.Stress.Tests.csproj::TargetFramework=netcoreapp3.1]
C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.SqlServer.Tests\DurableTask.SqlServer.Tests.csproj : error NU1903: Warning As Error: Package 'System.Data.SqlCli
ent' 4.8.5 has a known high severity vulnerability, https://github.com/advisories/GHSA-98g6-xh36-x2p7 [C:\Users\sky\work\code\durabletask\durabletask\DurableTask.sln]
    2 Warning(s)
    1 Error(s)
```

vs code 也会因为这个错误而无法解析项目。

### 解决方案

TBD

### 临时解决方案

修改 DurableTask.sln 文件，删除 DurableTask.SqlServer.Tests.csproj 这个项目：

``` bash
Project("{9A19103F-16F7-4668-BE54-9A1E7A4F7556}") = "DurableTask.SqlServer.Tests", "test\DurableTask.SqlServer.Tests\DurableTask.SqlServer.Tests.csproj", "{B835BFA6-D9BB-47C4-8594-38EAE0157BBA}"
EndProject
```

再删除 313 行：

```bash
		{B835BFA6-D9BB-47C4-8594-38EAE0157BBA} = {95C69A06-7F62-4652-A480-207B614C2869}
```



## build 结果



```bash
dotnet build
MSBuild version 17.9.6+a4ecab324 for .NET
  Determining projects to restore...
C:\Program Files\dotnet\sdk\8.0.203\Sdks\Microsoft.NET.Sdk\targets\Microsoft.NET.EolTargetFrameworks.targets(32,5): warning NETSDK1138: The target framework 'netcoreapp3.1' is 
out of support and will not receive security updates in the future. Please refer to https://aka.ms/dotnet-core-support for more information about the support policy. [C:\Users\
sky\work\code\durabletask\durabletask\samples\Correlation.Samples\Correlation.Samples.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Sdks\Microsoft.NET.Sdk\targets\Microsoft.NET.EolTargetFrameworks.targets(32,5): warning NETSDK1138: The target framework 'netcoreapp3.1' is 
out of support and will not receive security updates in the future. Please refer to https://aka.ms/dotnet-core-support for more information about the support policy. [C:\Users\
sky\work\code\durabletask\durabletask\test\DurableTask.Stress.Tests\DurableTask.Stress.Tests.csproj::TargetFramework=netcoreapp3.1]
  All projects are up-to-date for restore.
C:\Program Files\dotnet\sdk\8.0.203\Sdks\Microsoft.NET.Sdk\targets\Microsoft.NET.EolTargetFrameworks.targets(32,5): warning NETSDK1138: The target framework 'netcoreapp3.1' is 
out of support and will not receive security updates in the future. Please refer to https://aka.ms/dotnet-core-support for more information about the support policy. [C:\Users\
sky\work\code\durabletask\durabletask\test\DurableTask.Stress.Tests\DurableTask.Stress.Tests.csproj::TargetFramework=netcoreapp3.1]
C:\Program Files\dotnet\sdk\8.0.203\Sdks\Microsoft.NET.Sdk\targets\Microsoft.NET.EolTargetFrameworks.targets(32,5): warning NETSDK1138: The target framework 'netcoreapp3.1' is 
out of support and will not receive security updates in the future. Please refer to https://aka.ms/dotnet-core-support for more information about the support policy. [C:\Users\
sky\work\code\durabletask\durabletask\samples\Correlation.Samples\Correlation.Samples.csproj]
  DurableTask.Core -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.Core\bin\Debug\net462\DurableTask.Core.dll
  DurableTask.Core -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.Core\bin\Debug\netstandard2.0\DurableTask.Core.dll
  DurableTask.SqlServer -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.SqlServer\bin\Debug\net462\DurableTask.SqlServer.dll
  DurableTask.Emulator -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.Emulator\bin\Debug\net462\DurableTask.Emulator.dll
  DurableTask.Test.Orchestrations -> C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.Test.Orchestrations\bin\Debug\net462\DurableTask.Test.Orchestrations.dll
  The package Microsoft.Azure.DurableTask.Core.2.16.2 is missing a readme. Go to https://aka.ms/nuget/authoring-best-practices/readme to learn why package readmes are important
  .
  Successfully created package 'C:\Users\sky\work\code\durabletask\durabletask\build_output\packages\Microsoft.Azure.DurableTask.Core.2.16.2.nupkg'.
  Successfully created package 'C:\Users\sky\work\code\durabletask\durabletask\build_output\packages\Microsoft.Azure.DurableTask.Core.2.16.2.symbols.nupkg'.
  DurableTask.ServiceBus -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.ServiceBus\bin\Debug\net462\DurableTask.ServiceBus.dll
  DurableTask.ApplicationInsights -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.ApplicationInsights\bin\Debug\netstandard2.0\DurableTask.ApplicationInsights
  .dll
  DurableTask.Test.Orchestrations -> C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.Test.Orchestrations\bin\Debug\netstandard2.0\DurableTask.Test.Orchestration
  s.dll
  DurableTask.Redis -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.Redis\bin\Debug\netstandard2.0\DurableTask.Redis.dll
  DurableTask.SqlServer -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.SqlServer\bin\Debug\netstandard2.0\DurableTask.SqlServer.dll
  DurableTask.AzureServiceFabric -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.AzureServiceFabric\bin\x64\Debug\net462\DurableTask.AzureServiceFabric.dll
  DurableTask.AzureServiceFabric -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.AzureServiceFabric\bin\x64\Debug\net472\DurableTask.AzureServiceFabric.dll
  DurableTask.Emulator -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.Emulator\bin\Debug\netstandard2.0\DurableTask.Emulator.dll
  DurableTask.Core.Tests -> C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.Core.Tests\bin\Debug\net462\DurableTask.Core.Tests.dll
  DurableTask.AzureStorage -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.AzureStorage\bin\Debug\netstandard2.0\DurableTask.AzureStorage.dll
  TestApplication.Common -> C:\Users\sky\work\code\durabletask\durabletask\test\TestFabricApplication\TestApplication.Common\bin\x64\Debug\net472\TestApplication.Common.dll
  DurableTask.Emulator.Tests -> C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.Emulator.Tests\bin\Debug\net462\DurableTask.Emulator.Tests.dll
  DurableTask.ServiceBus -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.ServiceBus\bin\Debug\netstandard2.0\DurableTask.ServiceBus.dll
  DurableTask.AzureStorage -> C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.AzureStorage\bin\Debug\net462\DurableTask.AzureStorage.dll
  DurableTask.AzureServiceFabric.Tests -> C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Tests\bin\x64\Debug\net462\DurableTask.AzureService
  Fabric.Tests.dll
  TestApplication.Common -> C:\Users\sky\work\code\durabletask\durabletask\test\TestFabricApplication\TestApplication.Common\bin\x64\Debug\net462\TestApplication.Common.dll
  The package Microsoft.Azure.DurableTask.Redis.0.1.9-alpha is missing a readme. Go to https://aka.ms/nuget/authoring-best-practices/readme to learn why package readmes are imp
  ortant.
  Successfully created package 'C:\Users\sky\work\code\durabletask\durabletask\build_output\packages\Microsoft.Azure.DurableTask.Redis.0.1.9-alpha.nupkg'.
  DurableTask.ServiceBus.Tests -> C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.ServiceBus.Tests\bin\Debug\net462\DurableTask.ServiceBus.Tests.dll
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277: Found conflicts between different versions of "Newtonsoft.Json" that could
 not be resolved. [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric.Integration.Tests.csproj] 
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277: There was a conflict between "Newtonsoft.Json, Version=7.0.0.0, Culture=ne 
utral, PublicKeyToken=30ad4fe6b2a6aeed" and "Newtonsoft.Json, Version=12.0.0.0, Culture=neutral, PublicKeyToken=30ad4fe6b2a6aeed". [C:\Users\sky\work\code\durabletask\durableta 
sk\test\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:     "Newtonsoft.Json, Version=7.0.0.0, Culture=neutral, PublicKeyToken=30a 
d4fe6b2a6aeed" was chosen because it was primary and "Newtonsoft.Json, Version=12.0.0.0, Culture=neutral, PublicKeyToken=30ad4fe6b2a6aeed" was not. [C:\Users\sky\work\code\dura 
bletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:     References which depend on "Newtonsoft.Json, Version=7.0.0.0, Culture= 
neutral, PublicKeyToken=30ad4fe6b2a6aeed" [C:\Users\sky\.nuget\packages\newtonsoft.json\7.0.1\lib\net45\Newtonsoft.Json.dll]. [C:\Users\sky\work\code\durabletask\durabletask\te 
st\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:         C:\Users\sky\.nuget\packages\newtonsoft.json\7.0.1\lib\net45\Newto 
nsoft.Json.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric.Integration.Tests.csproj]    
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:           Project file item includes which caused reference "C:\Users\sky\ 
.nuget\packages\newtonsoft.json\7.0.1\lib\net45\Newtonsoft.Json.dll". [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\Dura 
bleTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:             C:\Users\sky\.nuget\packages\newtonsoft.json\7.0.1\lib\net45\N 
ewtonsoft.Json.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric.Integration.Tests.csproj 
]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:     References which depend on or have been unified to "Newtonsoft.Json, V 
ersion=12.0.0.0, Culture=neutral, PublicKeyToken=30ad4fe6b2a6aeed" []. [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\Dur 
ableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:         C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.Cor 
e\bin\Debug\net462\DurableTask.Core.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric.Int 
egration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:           Project file item includes which caused reference "C:\Users\sky\ 
work\code\durabletask\durabletask\src\DurableTask.Core\bin\Debug\net462\DurableTask.Core.dll". [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabr 
ic.Integration.Tests\DurableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:             C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask 
.Core\bin\Debug\net462\DurableTask.Core.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric 
.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:             C:\Users\sky\work\code\durabletask\durabletask\test\DurableTas 
k.Test.Orchestrations\bin\Debug\net462\DurableTask.Test.Orchestrations.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests 
\DurableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:             C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask 
.AzureServiceFabric\bin\x64\Debug\net462\DurableTask.AzureServiceFabric.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Test 
s\DurableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:             C:\Users\sky\work\code\durabletask\durabletask\test\TestFabric 
Application\TestApplication.Common\bin\x64\Debug\net462\TestApplication.Common.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integrati 
on.Tests\DurableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:         C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.Azu 
reServiceFabric\bin\x64\Debug\net462\DurableTask.AzureServiceFabric.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\Du 
rableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:           Project file item includes which caused reference "C:\Users\sky\ 
work\code\durabletask\durabletask\src\DurableTask.AzureServiceFabric\bin\x64\Debug\net462\DurableTask.AzureServiceFabric.dll". [C:\Users\sky\work\code\durabletask\durabletask\t 
est\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:             C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask 
.AzureServiceFabric\bin\x64\Debug\net462\DurableTask.AzureServiceFabric.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Test 
s\DurableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:         C:\Users\sky\.nuget\packages\microsoft.aspnet.webapi.client\5.2.6\ 
lib\net45\System.Net.Http.Formatting.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric.In 
tegration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:           Project file item includes which caused reference "C:\Users\sky\ 
.nuget\packages\microsoft.aspnet.webapi.client\5.2.6\lib\net45\System.Net.Http.Formatting.dll". [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFab 
ric.Integration.Tests\DurableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:             C:\Users\sky\.nuget\packages\microsoft.aspnet.webapi.client\5. 
2.6\lib\net45\System.Net.Http.Formatting.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabri 
c.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:             C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask 
.AzureServiceFabric\bin\x64\Debug\net462\DurableTask.AzureServiceFabric.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Test 
s\DurableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:             C:\Users\sky\.nuget\packages\microsoft.aspnet.webapi.core\5.2. 
6\lib\net45\System.Web.Http.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric.Integration 
.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:             C:\Users\sky\.nuget\packages\microsoft.aspnet.webapi.owin\5.2. 
6\lib\net45\System.Web.Http.Owin.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric.Integr 
ation.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:         C:\Users\sky\.nuget\packages\microsoft.aspnet.webapi.core\5.2.6\li 
b\net45\System.Web.Http.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric.Integration.Tes 
ts.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:           Project file item includes which caused reference "C:\Users\sky\ 
.nuget\packages\microsoft.aspnet.webapi.core\5.2.6\lib\net45\System.Web.Http.dll". [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integrati 
on.Tests\DurableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:             C:\Users\sky\.nuget\packages\microsoft.aspnet.webapi.core\5.2. 
6\lib\net45\System.Web.Http.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric.Integration 
.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:             C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask 
.AzureServiceFabric\bin\x64\Debug\net462\DurableTask.AzureServiceFabric.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Test 
s\DurableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:             C:\Users\sky\.nuget\packages\microsoft.aspnet.webapi.owin\5.2. 
6\lib\net45\System.Web.Http.Owin.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric.Integr 
ation.Tests.csproj]
  TestApplication.StatefulService -> C:\Users\sky\work\code\durabletask\durabletask\test\TestFabricApplication\TestApplication.StatefulService\bin\Debug\net472\TestApplication.
  StatefulService.exe
  DurableTask.Emulator.Tests -> C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.Emulator.Tests\bin\Debug\netcoreapp3.1\DurableTask.Emulator.Tests.dll
  Correlation.Samples -> C:\Users\sky\work\code\durabletask\durabletask\samples\Correlation.Samples\bin\Debug\netcoreapp3.1\Correlation.Samples.dll
  The package Microsoft.Azure.DurableTask.AzureServiceFabric.2.3.11 is missing a readme. Go to https://aka.ms/nuget/authoring-best-practices/readme to learn why package readmes
   are important.
  Successfully created package 'C:\Users\sky\work\code\durabletask\durabletask\build_output\packages\Microsoft.Azure.DurableTask.AzureServiceFabric.2.3.11.nupkg'.
  DurableTask.Redis.Tests -> C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.Redis.Tests\bin\Debug\netcoreapp3.1\DurableTask.Redis.Tests.dll
  Successfully created package 'C:\Users\sky\work\code\durabletask\durabletask\build_output\packages\Microsoft.Azure.DurableTask.AzureServiceFabric.2.3.11.symbols.nupkg'.
  DurableTask.AzureServiceFabric.Integration.Tests -> C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\bin\x64\Debug\net462\
  DurableTask.AzureServiceFabric.Integration.Tests.dll
C:\Users\sky\work\code\durabletask\durabletask\samples\DistributedTraceSample\OpenTelemetry\Program.cs(100,48): warning CS1998: This async method lacks 'await' operators and wi
ll run synchronously. Consider using the 'await' operator to await non-blocking API calls, or 'await Task.Run(...)' to do CPU-bound work on a background thread. [C:\Users\sky\w
ork\code\durabletask\durabletask\samples\DistributedTraceSample\OpenTelemetry\OpenTelemetrySample.csproj]
  DurableTask.ServiceBus.Tests -> C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.ServiceBus.Tests\bin\Debug\netcoreapp3.1\DurableTask.ServiceBus.Tests.dll
  OpenTelemetrySample -> C:\Users\sky\work\code\durabletask\durabletask\samples\DistributedTraceSample\OpenTelemetry\bin\Debug\net6.0\OpenTelemetrySample.dll
  ApplicationInsightsSample -> C:\Users\sky\work\code\durabletask\durabletask\samples\DistributedTraceSample\ApplicationInsights\bin\Debug\net6.0\ApplicationInsightsSample.dll
  DurableTask.Stress.Tests -> C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.Stress.Tests\bin\Debug\netcoreapp3.1\DurableTask.Stress.Tests.dll
  DurableTask.Core.Tests -> C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.Core.Tests\bin\Debug\netcoreapp3.1\DurableTask.Core.Tests.dll
  DurableTask.Stress.Tests -> C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.Stress.Tests\bin\Debug\net462\DurableTask.Stress.Tests.exe
  DurableTask.Samples -> C:\Users\sky\work\code\durabletask\durabletask\samples\DurableTask.Samples\bin\Debug\net462\DurableTask.Samples.exe
  The package Microsoft.Azure.DurableTask.AzureStorage.1.17.1 is missing a readme. Go to https://aka.ms/nuget/authoring-best-practices/readme to learn why package readmes are i
  mportant.
  Successfully created package 'C:\Users\sky\work\code\durabletask\durabletask\build_output\packages\Microsoft.Azure.DurableTask.AzureStorage.1.17.1.nupkg'.
  DurableTask.AzureStorage.Tests -> C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureStorage.Tests\bin\Debug\netcoreapp3.1\DurableTask.AzureStorage.Tests.dl
  l
  DurableTask.AzureStorage.Tests -> C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureStorage.Tests\bin\Debug\net462\DurableTask.AzureStorage.Tests.dll

Build succeeded.

C:\Program Files\dotnet\sdk\8.0.203\Sdks\Microsoft.NET.Sdk\targets\Microsoft.NET.EolTargetFrameworks.targets(32,5): warning NETSDK1138: The target framework 'netcoreapp3.1' is  
out of support and will not receive security updates in the future. Please refer to https://aka.ms/dotnet-core-support for more information about the support policy. [C:\Users\ 
sky\work\code\durabletask\durabletask\samples\Correlation.Samples\Correlation.Samples.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Sdks\Microsoft.NET.Sdk\targets\Microsoft.NET.EolTargetFrameworks.targets(32,5): warning NETSDK1138: The target framework 'netcoreapp3.1' is  
out of support and will not receive security updates in the future. Please refer to https://aka.ms/dotnet-core-support for more information about the support policy. [C:\Users\ 
sky\work\code\durabletask\durabletask\test\DurableTask.Stress.Tests\DurableTask.Stress.Tests.csproj::TargetFramework=netcoreapp3.1]
C:\Program Files\dotnet\sdk\8.0.203\Sdks\Microsoft.NET.Sdk\targets\Microsoft.NET.EolTargetFrameworks.targets(32,5): warning NETSDK1138: The target framework 'netcoreapp3.1' is  
out of support and will not receive security updates in the future. Please refer to https://aka.ms/dotnet-core-support for more information about the support policy. [C:\Users\ 
sky\work\code\durabletask\durabletask\test\DurableTask.Stress.Tests\DurableTask.Stress.Tests.csproj::TargetFramework=netcoreapp3.1]
C:\Program Files\dotnet\sdk\8.0.203\Sdks\Microsoft.NET.Sdk\targets\Microsoft.NET.EolTargetFrameworks.targets(32,5): warning NETSDK1138: The target framework 'netcoreapp3.1' is  
out of support and will not receive security updates in the future. Please refer to https://aka.ms/dotnet-core-support for more information about the support policy. [C:\Users\ 
sky\work\code\durabletask\durabletask\samples\Correlation.Samples\Correlation.Samples.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277: Found conflicts between different versions of "Newtonsoft.Json" that could 
 not be resolved. [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric.Integration.Tests.csproj] 
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277: There was a conflict between "Newtonsoft.Json, Version=7.0.0.0, Culture=ne 
utral, PublicKeyToken=30ad4fe6b2a6aeed" and "Newtonsoft.Json, Version=12.0.0.0, Culture=neutral, PublicKeyToken=30ad4fe6b2a6aeed". [C:\Users\sky\work\code\durabletask\durableta 
sk\test\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:     "Newtonsoft.Json, Version=7.0.0.0, Culture=neutral, PublicKeyToken=30a 
d4fe6b2a6aeed" was chosen because it was primary and "Newtonsoft.Json, Version=12.0.0.0, Culture=neutral, PublicKeyToken=30ad4fe6b2a6aeed" was not. [C:\Users\sky\work\code\dura 
bletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:     References which depend on "Newtonsoft.Json, Version=7.0.0.0, Culture= 
neutral, PublicKeyToken=30ad4fe6b2a6aeed" [C:\Users\sky\.nuget\packages\newtonsoft.json\7.0.1\lib\net45\Newtonsoft.Json.dll]. [C:\Users\sky\work\code\durabletask\durabletask\te 
st\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:         C:\Users\sky\.nuget\packages\newtonsoft.json\7.0.1\lib\net45\Newto 
nsoft.Json.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric.Integration.Tests.csproj]    
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:           Project file item includes which caused reference "C:\Users\sky\ 
.nuget\packages\newtonsoft.json\7.0.1\lib\net45\Newtonsoft.Json.dll". [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\Dura 
bleTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:             C:\Users\sky\.nuget\packages\newtonsoft.json\7.0.1\lib\net45\N 
ewtonsoft.Json.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric.Integration.Tests.csproj 
]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:     References which depend on or have been unified to "Newtonsoft.Json, V 
ersion=12.0.0.0, Culture=neutral, PublicKeyToken=30ad4fe6b2a6aeed" []. [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\Dur 
ableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:         C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.Cor 
e\bin\Debug\net462\DurableTask.Core.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric.Int 
egration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:           Project file item includes which caused reference "C:\Users\sky\ 
work\code\durabletask\durabletask\src\DurableTask.Core\bin\Debug\net462\DurableTask.Core.dll". [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabr 
ic.Integration.Tests\DurableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:             C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask 
.Core\bin\Debug\net462\DurableTask.Core.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric 
.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:             C:\Users\sky\work\code\durabletask\durabletask\test\DurableTas 
k.Test.Orchestrations\bin\Debug\net462\DurableTask.Test.Orchestrations.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests 
\DurableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:             C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask 
.AzureServiceFabric\bin\x64\Debug\net462\DurableTask.AzureServiceFabric.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Test 
s\DurableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:             C:\Users\sky\work\code\durabletask\durabletask\test\TestFabric 
Application\TestApplication.Common\bin\x64\Debug\net462\TestApplication.Common.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integrati 
on.Tests\DurableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:         C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask.Azu 
reServiceFabric\bin\x64\Debug\net462\DurableTask.AzureServiceFabric.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\Du 
rableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:           Project file item includes which caused reference "C:\Users\sky\ 
work\code\durabletask\durabletask\src\DurableTask.AzureServiceFabric\bin\x64\Debug\net462\DurableTask.AzureServiceFabric.dll". [C:\Users\sky\work\code\durabletask\durabletask\t 
est\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:             C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask 
.AzureServiceFabric\bin\x64\Debug\net462\DurableTask.AzureServiceFabric.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Test 
s\DurableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:         C:\Users\sky\.nuget\packages\microsoft.aspnet.webapi.client\5.2.6\ 
lib\net45\System.Net.Http.Formatting.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric.In 
tegration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:           Project file item includes which caused reference "C:\Users\sky\ 
.nuget\packages\microsoft.aspnet.webapi.client\5.2.6\lib\net45\System.Net.Http.Formatting.dll". [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFab 
ric.Integration.Tests\DurableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:             C:\Users\sky\.nuget\packages\microsoft.aspnet.webapi.client\5. 
2.6\lib\net45\System.Net.Http.Formatting.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabri
c.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:             C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask 
.AzureServiceFabric\bin\x64\Debug\net462\DurableTask.AzureServiceFabric.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Test 
s\DurableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:             C:\Users\sky\.nuget\packages\microsoft.aspnet.webapi.core\5.2. 
6\lib\net45\System.Web.Http.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric.Integration 
.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:             C:\Users\sky\.nuget\packages\microsoft.aspnet.webapi.owin\5.2. 
6\lib\net45\System.Web.Http.Owin.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric.Integr 
ation.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:         C:\Users\sky\.nuget\packages\microsoft.aspnet.webapi.core\5.2.6\li 
b\net45\System.Web.Http.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric.Integration.Tes 
ts.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:           Project file item includes which caused reference "C:\Users\sky\ 
.nuget\packages\microsoft.aspnet.webapi.core\5.2.6\lib\net45\System.Web.Http.dll". [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integrati 
on.Tests\DurableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:             C:\Users\sky\.nuget\packages\microsoft.aspnet.webapi.core\5.2. 
6\lib\net45\System.Web.Http.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric.Integration 
.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:             C:\Users\sky\work\code\durabletask\durabletask\src\DurableTask 
.AzureServiceFabric\bin\x64\Debug\net462\DurableTask.AzureServiceFabric.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Test 
s\DurableTask.AzureServiceFabric.Integration.Tests.csproj]
C:\Program Files\dotnet\sdk\8.0.203\Microsoft.Common.CurrentVersion.targets(2389,5): warning MSB3277:             C:\Users\sky\.nuget\packages\microsoft.aspnet.webapi.owin\5.2. 
6\lib\net45\System.Web.Http.Owin.dll [C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.AzureServiceFabric.Integration.Tests\DurableTask.AzureServiceFabric.Integr 
ation.Tests.csproj]
C:\Users\sky\work\code\durabletask\durabletask\samples\DistributedTraceSample\OpenTelemetry\Program.cs(100,48): warning CS1998: This async method lacks 'await' operators and wi 
ll run synchronously. Consider using the 'await' operator to await non-blocking API calls, or 'await Task.Run(...)' to do CPU-bound work on a background thread. [C:\Users\sky\w 
ork\code\durabletask\durabletask\samples\DistributedTraceSample\OpenTelemetry\OpenTelemetrySample.csproj]
    6 Warning(s)
    0 Error(s)

Time Elapsed 00:00:05.24
```


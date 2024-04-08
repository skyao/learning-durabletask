---
title: "本地发布"
linkTitle: "本地发布"
weight: 130
date: 2024-01-19
description: >
  在本地发布包并在其他项目使用
---

## 发布到本地



- [How To Release a NuGet Package Locally? - Full Duck Dev](https://fullduck.dev/how-to-release-a-nuget-package-locally/)



## 实际操作

### durabletask-dotnet 项目

修改 eng\targets\Release.props ，从版本号从 1.2.2：

```bash
<VersionPrefix>1.2.2</VersionPrefix>
<VersionSuffix></VersionSuffix>
```

修改为 1.2.3-alpha

```bash
    <VersionPrefix>1.2.3</VersionPrefix>
    <VersionSuffix>alpha</VersionSuffix>
```

修改代码，构建项目：

```bash
# git submodule update --init --recursive
# git submodule update --remote: see https://stackoverflow.org.cn/questions/1777854
# cd ~/work/code/durabletask-fork/durabletask-dotnet
rm -rf out/bin
rm -rf out/pkg
dotnet build
dotnet pack
```

看到输出:

```bash
PS C:\Users\sky\work\code\durabletask-fork\durabletask-dotnet> dotnet pack
用于 .NET MSBuild 版本 17.9.6+a4ecab324
  正在确定要还原的项目…
  所有项目均是最新的，无法还原。
  Generators -> C:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\out\bin\Release\Generators\netstandard2.0\Microsoft.DurableTask.Generators.dll
  Abstractions -> C:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\out\bin\Release\Abstractions\netstandard2.0\Microsoft.DurableTask.Abstractions.dll
  已成功创建包“C:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\out/pkg/Microsoft.DurableTask.Generators.1.0.0-preview.1.nupkg”。
  Grpc -> C:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\out\bin\Release\Grpc\netstandard2.0\Microsoft.DurableTask.Grpc.dll
  Grpc -> C:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\out\bin\Release\Grpc\net6.0\Microsoft.DurableTask.Grpc.dll
  已成功创建包“C:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\out/pkg/Microsoft.DurableTask.Abstractions.1.2.3-alpha.nupkg”。
  已成功创建包“C:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\out/pkg/Microsoft.DurableTask.Abstractions.1.2.3-alpha.snupkg”。
  Client -> C:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\out\bin\Release\Client\netstandard2.0\Microsoft.DurableTask.Client.dll
  Worker -> C:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\out\bin\Release\Worker\netstandard2.0\Microsoft.DurableTask.Worker.dll
  已成功创建包“C:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\out/pkg/Microsoft.DurableTask.Grpc.1.2.3-alpha.nupkg”。
  已成功创建包“C:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\out/pkg/Microsoft.DurableTask.Grpc.1.2.3-alpha.snupkg”。
  已成功创建包“C:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\out/pkg/Microsoft.DurableTask.Client.1.2.3-alpha.nupkg”。
  已成功创建包“C:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\out/pkg/Microsoft.DurableTask.Client.1.2.3-alpha.snupkg”。
  已成功创建包“C:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\out/pkg/Microsoft.DurableTask.Worker.1.2.3-alpha.nupkg”。
  已成功创建包“C:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\out/pkg/Microsoft.DurableTask.Worker.1.2.3-alpha.snupkg”。
  Worker.Grpc -> C:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\out\bin\Release\Worker.Grpc\net6.0\Microsoft.DurableTask.Worker.Grpc.dll
  Worker.Grpc -> C:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\out\bin\Release\Worker.Grpc\netstandard2.0\Microsoft.DurableTask.Worker.Grpc.dll
  Client.OrchestrationServiceClientShim -> C:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\out\bin\Release\Client.OrchestrationServiceClientShim\netstandard
  2.0\Microsoft.DurableTask.Client.OrchestrationServiceClientShim.dll
  Client.Grpc -> C:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\out\bin\Release\Client.Grpc\netstandard2.0\Microsoft.DurableTask.Client.Grpc.dll
C:\Program Files\dotnet\sdk\8.0.203\Sdks\NuGet.Build.Tasks.Pack\build\NuGet.Build.Tasks.Pack.targets(221,5): warning NU5104: 包的稳定版本不应有预发布依赖项。请修改依赖
项“Microsoft.DurableTa
sk.Client [1.2.3-alpha, )”的版本规范，或更新 nuspec 中的版本字段。 [C:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\src\Client\OrchestrationServiceClientShim\Client.Orchest
rationServiceClientShim.csproj]
  已成功创建包“C:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\out/pkg/Microsoft.DurableTask.Client.OrchestrationServiceClientShim.1.0.5.nupkg”。
  已成功创建包“C:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\out/pkg/Microsoft.DurableTask.Client.OrchestrationServiceClientShim.1.0.5.snupkg”。
  Client.Grpc -> C:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\out\bin\Release\Client.Grpc\net6.0\Microsoft.DurableTask.Client.Grpc.dll
  已成功创建包“C:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\out/pkg/Microsoft.DurableTask.Worker.Grpc.1.2.3-alpha.nupkg”。
  已成功创建包“C:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\out/pkg/Microsoft.DurableTask.Worker.Grpc.1.2.3-alpha.snupkg”。
  已成功创建包“C:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\out/pkg/Microsoft.DurableTask.Client.Grpc.1.2.3-alpha.nupkg”。
  已成功创建包“C:\Users\sky\work\code\durabletask-fork\durabletask-dotnet\out/pkg/Microsoft.DurableTask.Client.Grpc.1.2.3-alpha.snupkg”。
```

nupkg 和 package 之间的对应关系：

| nupkg file                                                   | package                             |
| ------------------------------------------------------------ | ----------------------------------- |
| Microsoft.DurableTask.Abstractions.1.2.3.nupkg               |                                     |
|                                                              |                                     |
| Microsoft.DurableTask.Client.1.2.3.nupkg                     |                                     |
| Microsoft.DurableTask.Client.Grpc.1.2.3.nupkg                | `microsoft.durabletask.client.grpc` |
| Microsoft.DurableTask.Client.OrchestrationServiceClientShim.1.0.5.nupkg |                                     |
|                                                              |                                     |
| Microsoft.DurableTask.Generators.1.0.0-preview.1.nupkg       |                                     |
|                                                              |                                     |
| Microsoft.DurableTask.Grpc.1.2.3.nupkg                       | `microsoft.durabletask.grpc`        |
|                                                              |                                     |
| Microsoft.DurableTask.Worker.Grpc.1.2.3.nupkg                | `microsoft.durabletask.worker.grpc` |
| Microsoft.DurableTask.Worker.1.2.3.nupkg                     | `microsoft.durabletask.worker`      |

发布文件到 nuget 本地仓库：

```bash
rm -rf "C:\soft\nuget-local-package\microsoft.durabletask.abstractions"
rm -rf "C:\soft\nuget-local-package\microsoft.durabletask.client"
rm -rf "C:\soft\nuget-local-package\microsoft.durabletask.client.grpc"
rm -rf "C:\soft\nuget-local-package\microsoft.durabletask.grpc"
rm -rf "C:\soft\nuget-local-package\microsoft.durabletask.worker"
rm -rf "C:\soft\nuget-local-package\microsoft.durabletask.worker.grpc"

rm -rf "C:\Users\sky\.nuget\packages\microsoft.durabletask.abstractions"
rm -rf "C:\Users\sky\.nuget\packages\microsoft.durabletask.client"
rm -rf "C:\Users\sky\.nuget\packages\microsoft.durabletask.client.grpc"
# rm -rf "C:\Users\sky\.nuget\packages\microsoft.durabletask.generators"
rm -rf "C:\Users\sky\.nuget\packages\microsoft.durabletask.grpc"
# rm -rf "C:\Users\sky\.nuget\packages\microsoft.durabletask.sidecar"
# rm -rf "C:\Users\sky\.nuget\packages\microsoft.durabletask.sidecar.protobuf"
rm -rf "C:\Users\sky\.nuget\packages\microsoft.durabletask.worker"
rm -rf "C:\Users\sky\.nuget\packages\microsoft.durabletask.worker.grpc"

nuget add out/pkg/Microsoft.DurableTask.Abstractions.1.2.3-alpha.nupkg -source "C:\soft\nuget-local-package"
nuget add out/pkg/Microsoft.DurableTask.Client.1.2.3-alpha.nupkg -source "C:\soft\nuget-local-package"
nuget add out/pkg/Microsoft.DurableTask.Worker.1.2.3-alpha.nupkg -source "C:\soft\nuget-local-package"
nuget add out/pkg/Microsoft.DurableTask.Grpc.1.2.3-alpha.nupkg -source "C:\soft\nuget-local-package"
nuget add out/pkg/Microsoft.DurableTask.Client.Grpc.1.2.3-alpha.nupkg -source "C:\soft\nuget-local-package"
nuget add out/pkg/Microsoft.DurableTask.Worker.Grpc.1.2.3-alpha.nupkg -source "C:\soft\nuget-local-package"
```



### azure-functions-durable-extension 项目

修改 `src\Worker.Extensions.DurableTask\Worker.Extensions.DurableTask.csproj` 文件，

```bash
  <ItemGroup>
    ......
    <PackageReference Include="Microsoft.DurableTask.Client.Grpc" Version="1.2.2" />
    <PackageReference Include="Microsoft.DurableTask.Worker.Grpc" Version="1.2.2" />
  </ItemGroup>
```

修改为 

```bash
  <ItemGroup>
    ......
    <PackageReference Include="Microsoft.DurableTask.Client.Grpc" Version="1.2.3-alpha" />
    <PackageReference Include="Microsoft.DurableTask.Worker.Grpc" Version="1.2.3-alpha" />
  </ItemGroup>
```

修改 `nuget.config` 文件

```xml
<configuration>
  <packageSources>
    ......
    <add key="nugetlocal" value="C:\soft\nuget-local-package" />
  </packageSources>
</configuration>
```

这个项目也需要打包给其他项目用，因此类似的也需要修改版本，打开 `src\Worker.Extensions.DurableTask\Worker.Extensions.DurableTask.csproj` 文件

```xml
    <!-- Version information -->
    <VersionPrefix>1.1.2</VersionPrefix>
    <VersionSuffix></VersionSuffix>
```

修改为

```xml
    <!-- Version information -->
    <VersionPrefix>1.1.3</VersionPrefix>
    <VersionSuffix>alpha</VersionSuffix>
```

之后就可以执行 

```bash
dotnet build
```

命令来打包

```bash
rm -rf "C:\soft\nuget-local-package\microsoft.azure.functions.worker.extensions.durabletask"
rm -rf "C:\Users\sky\.nuget\packages\microsoft.azure.functions.worker.extensions.durabletas"

nuget add "src\Worker.Extensions.DurableTask\bin\Debug\Microsoft.Azure.Functions.Worker.Extensions.DurableTask.1.1.3-alpha.nupkg" -source "C:\soft\nuget-local-package"
```



### quickstart 项目

在项目根目录下增加 `nuget.config` 文件，内容为：

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <packageSources>
    <clear/>
    <add key="nugetlocal" value="C:\soft\nuget-local-package" />
  </packageSources>
</configuration>
```

修改项目的 csproj 文件，如 MyDurableFunction1.csproj 

```xml
    <PackageReference Include="Microsoft.Azure.Functions.Worker.Extensions.DurableTask" Version="1.1.2" />
```

修改为 

```bash
    <PackageReference Include="Microsoft.Azure.Functions.Worker.Extensions.DurableTask" Version="1.1.3-alpha" />
```

构建之前，最好先删除掉 "C:\Users\sky\.nuget\packages\" 下要用的依赖（命令在上面），避免缓存造成版本没有更新。


---
title: "nuget 401"
linkTitle: "nuget 401"
weight: 190
date: 2024-01-19
description: >
  nuget 401
---



### 报错

构建时突然遇到 401 (Unauthorized) 错误，然后就无法继续构建：

```bash
dotnet build
适用于 .NET MSBuild 版本 17.9.6+a4ecab324
  正在确定要还原的项目…
C:\Users\sky\work\code\durabletask\durabletask-dotnet\test\Generators.Tests\Generators.Tests.csproj : error NU1301: 无法加载源 https://pkgs.dev.azure.com/azfunc/e6a70c92-412
8-43
9f-8012-382fe78d6396/_packaging/AzureFunctionsTempStaging/nuget/v3/index.json 的服务索引。 [C:\Users\sky\work\code\durabletask\durabletask-dotnet\Microsoft.DurableTask.sln]
  未能还原 C:\Users\sky\work\code\durabletask\durabletask-dotnet\test\Generators.Tests\Generators.Tests.csproj (用时 15.28 sec)。
C:\Program Files\dotnet\sdk\8.0.203\NuGet.targets(169,5): error : 无法加载源 https://pkgs.dev.azure.com/azfunc/e6a70c92-4128-439f-8012-382fe78d6396/_packaging/AzureFunctions
Temp
Staging/nuget/v3/index.json 的服务索引。 [C:\Users\sky\work\code\durabletask\durabletask-dotnet\Microsoft.DurableTask.sln]
C:\Program Files\dotnet\sdk\8.0.203\NuGet.targets(169,5): error :   Response status code does not indicate success: 401 (Unauthorized). [C:\Users\sky\work\code\durabletask\ 
durabletask-dotnet\Microsoft.DurableTask.sln]
```

访问地址 `https://pkgs.dev.azure.com/azfunc/e6a70c92-4128-439f-8012-382fe78d6396/_packaging/AzureFunctions
Temp
Staging/nuget/v3/index.json` 时报错 401:

```bash
Response status code does not indicate success: 401 (Unauthorized)
```





```bash
dotnet build --interactive
适用于 .NET MSBuild 版本 17.9.6+a4ecab324
  正在确定要还原的项目…
      [CredentialProvider]DeviceFlow: https://pkgs.dev.azure.com/azfunc/e6a70c92-4128-439f-8012-382fe78d6396/_packaging/AzureFunctionsTempStaging/nuget/v3/index.json
      [CredentialProvider]ATTENTION: User interaction required.

      **********************************************************************

      To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code IDJNNR9QS to authenticate.

      **********************************************************************

      [CredentialProvider]DeviceFlow: https://pkgs.dev.azure.com/azfunc/e6a70c92-4128-439f-8012-382fe78d6396/_packaging/AzureFunctionsTempStaging/nuget/v3/index.json
      [CredentialProvider]ATTENTION: User interaction required.

      **********************************************************************

      To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code IZGMCADU2 to authenticate.

      **********************************************************************

```










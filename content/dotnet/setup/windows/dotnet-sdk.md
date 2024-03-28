---
title: "安装.NET SDK"
linkTitle: ".NET SDK"
weight: 110
date: 2024-01-19
description: >
  在 windows 上安装.NET SDK
---



###  .NET 6.0 SDK 

https://dotnet.microsoft.com/en-us/download/dotnet/6.0

下载安装 .NET 6.0 SDK 

```bash
下列项安装于: ""
    • .NET SDK 6.0.420
    • .NET Runtime 6.0.28
    • ASP.NET Core Runtime 6.0.28
    • .NET Windows Desktop Runtime 6.0.28
```

### .NET 8.0 SDK

https://dotnet.microsoft.com/zh-cn/download/dotnet/sdk-for-vs-code?utm_source=vs-code&amp;utm_medium=referral&amp;utm_campaign=sdk-install'

.NET 8.0 SDK

下载到 dotnet-sdk-8.0.203-win-x64.exe ，执行安装，但是不知道安装到哪里去了。。。

```bash
下列产品已安装: 
    • .NET SDK 8.0.203
    • .NET Runtime 8.0.3
    • ASP.NET Core Runtime 8.0.3
    • .NET Windows Desktop Runtime 8.0.3
```

查看一下：

```bash
$ dotnet --version
8.0.203

$ which dotnet
/c/Program Files/dotnet/dotnet

$ echo $PATH
/c/Users/sky/bin:/mingw64/bin:/usr/local/bin:/usr/bin:/bin:/mingw64/bin:/usr/bin:/c/Users/sky/bin:/c/Windows/system32:/c/Windows:/c/Windows/System32/Wbem:/c/Windows/System32/WindowsPowerShell/v1.0:/c/Windows/System32/OpenSSH:/cmd:/c/Program Files/dotnet:/c/Users/sky/AppData/Local/Microsoft/WindowsApps:/bin:/c/Users/sky/.dotnet/tools:/usr/bin/vendor_perl:/usr/bin/core_perl

```

PATH 中增加了两个路径：

- `/c/Program Files/dotnet`
- `/c/Users/sky/.dotnet/tools`



### .NET Core 3.1 Runtime

还必须安装这个 runtime，虽然已经被标注为 此版本已过期：

```bash
Testhost process for source(s) 'C:\Users\sky\work\code\durabletask\durabletask\Test\DurableTask.Core.Tests\bin\Debug\netcoreapp3.1\DurableTask.Core.Tests.dll' exited with error: You must install or update .NET to run this application.
App: C:\Users\sky\.nuget\packages\microsoft.testplatform.testhost\15.9.0\lib\netstandard1.5\testhost.dll
Architecture: x64
Framework: 'Microsoft.NETCore.App', version '3.1.0' (x64)
.NET location: C:\Program Files\dotnet\
The following frameworks were found:
  6.0.28 at [C:\Program Files\dotnet\shared\Microsoft.NETCore.App]
  8.0.3 at [C:\Program Files\dotnet\shared\Microsoft.NETCore.App]
Learn more:
https://aka.ms/dotnet/app-launch-failed
To install missing framework, download:
https://aka.ms/dotnet-core-applaunch?framework=Microsoft.NETCore.App&framework_version=3.1.0&arch=x64&rid=win-x64&os=win10
. Please check the diagnostic logs for more information.
```

下载地址：

https://aka.ms/dotnet-core-applaunch?framework=Microsoft.NETCore.App&framework_version=3.1.0&arch=x64&rid=win-x64&os=win10

下载得到 dotnet-runtime-3.1.32-win-x64.exe 文件，安装即可。


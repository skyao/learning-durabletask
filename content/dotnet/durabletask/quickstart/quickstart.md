---
title: "DurableTask quickstart"
linkTitle: "Quickstart"
weight: 10
date: 2024-01-19
description: >
  运行durabletask Core的Quickstart
---



以 `DurableTask.Samples` 为例



## 准备工作

### azure storage

为了运行 sample，必须先准备好 azure storage，然后修改 `samples\DurableTask.Samples\App.config` 文件中的 StorageConnectionString: 

```xml
  <appSettings>
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=skyversioningdev;AccountKey=5dxW9E4xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx;EndpointSuffix=core.windows.net" />
    <add key="taskHubName" value="SamplesHub" />
    <add key="SmtpNetworkCredentials" value="" />
  </appSettings>
```

### dotnet 4.6.2 版本

打开 `samples\DurableTask.Samples\DurableTask.Samples.csproj` 可以看到对 .net 的要求是 .net 4.6.2

```xml
  <PropertyGroup>
    <AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
    <OutputType>Exe</OutputType>
    <TargetFramework>net462</TargetFramework>
  </PropertyGroup>
```

用 .net 8.0 可以正常运行，不用单独再安装 .net 4.6.2，但如果要 debug，则必须再单独安装  .net 4.6.2

> TBD：要不要安装  .net 4.6.2 后面再验证一下。

## 运行 samples

### 构建

```bash
samples\DurableTask.Samples\

dotnet build
```

### 运行

以 Greetings sample 为例：

```bash
dotnet run -s Greetings
```

注意这个sample要求弹出一个窗口，因此不能在无界面的纯命令行下， 需要在 windows 界面下用 cmd 或者 bash 操作。否则会报错：

```json
EventId : 4, Level : Warning, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : abdb59a4-a456-4b14-b1d6-c77a277f9199] [executionId : d23194
e7550c4f8da41d333911c80647] [sessionId : ] [message : Instance Id '[InstanceId: abdb59a4-a456-4b14-b1d6-c77a277f9199, ExecutionId: d23194e7550c4f8da41d333911c80647]' complet
ed in state Failed with result: Showing a modal dialog box or form when the application is not running in UserInteractive mode is not a valid operation. Specify the ServiceN
otification or DefaultDesktopOnly style to display a notification from a service application.] [exception : ] [eventType : TaskOrchestrationDispatcher-InstanceCompleted] , E
ventName : WarningInfo, Timestamp : 2024-04-28T01:39:38.0885765Z, ProcessId : 5716, ThreadId : 2952, ActivityId : d03ff6a5-eafb-4577-8b61-89cd486db722

"Reason": "Showing a modal dialog box or form when the application is not running in UserInteractive mode is not a valid operation. Specify the ServiceNotification or 
DefaultDesktopOnly style to display a notification from a service application.",
```



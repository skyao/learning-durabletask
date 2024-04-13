---
title: "生成protobuf文件"
linkTitle: "protobuf"
weight: 200
date: 2024-01-19
description: >
  在 azure-functions-dotnet-worker 项目中更新protobuf并生成代码
---

参考： https://github.com/Azure/azure-functions-dotnet-worker/tree/main/protos/azure-functions-language-worker-protobuf

## 准备

### grpc.tools

```bash
nuget.exe install grpc.tools -Version 2.60.0
```

### Google.Protobuf.Tools

```bash
nuget.exe install Google.Protobuf.Tools -Version 3.26.1
```

### 准备环境变量和目录



在 windows cmd 下执行:

```bash
cd C:\Users\sky\work\code\durabletask-fork\azure-functions-dotnet-worker\protos

set NUGET_PATH="%UserProfile%\.nuget\packages"
set GRPC_TOOLS_PATH=%NUGET_PATH%\grpc.tools\2.60.0\tools\windows_x86
set PROTO_PATH=.\azure-functions-language-worker-protobuf\src\proto
set PROTO=.\azure-functions-language-worker-protobuf\src\proto\FunctionRpc.proto
set PROTOBUF_TOOLS=%NUGET_PATH%\google.protobuf.tools\3.26.1\tools
set MSGDIR=.\Messages

if exist %MSGDIR% rmdir /s /q %MSGDIR%
mkdir %MSGDIR%

set OUTDIR=%MSGDIR%\DotNet
mkdir %OUTDIR%
```



## 生成

在 windows cmd 下执行: 

```bash
%GRPC_TOOLS_PATH%\protoc.exe %PROTO% --csharp_out %OUTDIR% --grpc_out=%OUTDIR% --plugin=protoc-gen-grpc=%GRPC_TOOLS_PATH%\grpc_csharp_plugin.exe --proto_path=%PROTO_PATH% --proto_path=%PROTOBUF_TOOLS%
```

会得到两个生成的文件：

- protos\Messages\DotNet\FunctionRpc.cs

- protos\Messages\DotNet\FunctionRpcGrpc.cs



> 备注：发现不需要这样生成，直接 dotnet build / dotnet pack 后就会自动更新。

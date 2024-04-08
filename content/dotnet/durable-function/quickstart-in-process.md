---
title: "durable functions quickstart"
linkTitle: "quickstart"
weight: 100
date: 2024-01-19
description: >
  durable functions quickstart
---



参考：

- [Create your first durable function in Azure using C# | Microsoft Learn](https://learn.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-create-first-csharp?pivots=code-editor-vscode)

## 文档

Durable Functions 是 Azure Functions 的扩展，可让您在无服务器环境中编写有状态函数。该扩展可为您管理状态、检查点和重启。

在本文中，您将学习如何使用 Visual Studio Code 本地创建和测试 "hello world "持久函数。该函数协调和连锁调用其他函数。然后，您就可以将函数代码发布到 Azure。这些工具是 Visual Studio Code Azure 函数扩展的一部分。

### 前提条件

要完成本教程

- 安装 Visual Studio Code。

- 安装以下 Visual Studio Code 扩展：

  - Azure 函数

  - C#

- 确保拥有最新版本的 Azure Functions 核心工具。

- Durable Functions 需要一个 Azure 存储账户。你需要一个 Azure 订阅。

- 确保安装了 3.1 版或更高版本的 .NET Core SDK。



## 实操



### 准备工作

#### 安装 vs code 插件

- Azure Functions

- C#

#### 安装 Azure Functions Core Tools

https://go.microsoft.com/fwlink/?linkid=2174087 

安装完成之后，用 windows 的 cmd 就可以使用 func 命令了：

```bash
func --version
4.0.5611
```

此时用 git bash 或者 zsh 也能使用 func 命令:

```bash
which func
/c/Program Files/Microsoft/Azure Functions Core Tools/func
func --version
4.0.5611
```

但是，vs code 下的 shell 不能访问 func。需要 修改 zsh 

```bash
vi ~/.zshrc
```

加入 PATH

```bash
# azure functions
export PATH=$PATH:"/c/Program Files/Microsoft/Azure Functions Core Tools/"
```

再 `source  ~/.zshrc` 就可以了。



### 创建本地项目

参考：

https://learn.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-create-first-csharp?pivots=code-editor-vscode#create-an-azure-functions-project



### 添加 function 到应用



https://learn.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-create-first-csharp?pivots=code-editor-vscode#add-functions-to-the-app

报错：

```bash
Detected package downgrade: Microsoft.Azure.Functions.Worker.Extensions.Http from 3.1.0 to 3.0.13. Reference the package directly from the project to select a different version.  
```

修改 `mydurablefunctions1.csproj` 文件，将

```xml
<PackageReference Include="Microsoft.Azure.Functions.Worker.Extensions.Http" Version="3.0.13" />
```

修改为：

```xml
<PackageReference Include="Microsoft.Azure.Functions.Worker.Extensions.Http" Version="3.1.0" />
```

关闭 vs code，再重新打开这个项目。

然后就是弹出 select a storage account 时，选择了  azure storage account，但是很奇怪没有弹出文档中所说的各种提示。导致无法按照文档操作，只能手工创建  azure storage account



### 手工创建 azure storage account

打开 azure 页面，手工创建 azure storage account：

- resource group:  versioning-dev

- storage account name:  skyversioningdev

- Region: east asia

创建完成后，打开 "Accdess keys"，就能看到key，类似：

```bash
DefaultEndpointsProtocol=https;AccountName=skyversioningdev;AccountKey=5dx xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxEvy2+AStFSJ8zg==;EndpointSuffix=core.windows.net
```

修改 local.settings.json 文件，将原本为空的 AzureWebJobsStorage 设置为：

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=skyversioningdev;AccountKey=5dx xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxEvy2+AStFSJ8zg==;EndpointSuffix=core.windows.net",
    "FUNCTIONS_WORKER_RUNTIME": "dotnet-isolated"
  }
}
```

然后再手工添加 DurableTask package 到项目中，执行命令：

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.DurableTask --version 2.13.1
```

这时打开 mydurablefunctions1.csproj 就能看到了：

```xml
  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.Azure.Functions.Worker" Version="1.20.1" />
    <PackageReference Include="Microsoft.Azure.Functions.Worker.Extensions.DurableTask" Version="1.0.0" />
    <PackageReference Include="Microsoft.Azure.Functions.Worker.Extensions.Http" Version="3.1.0" />
    <PackageReference Include="Microsoft.Azure.Functions.Worker.Extensions.Http.AspNetCore" Version="1.2.0" />
    <PackageReference Include="Microsoft.Azure.Functions.Worker.Sdk" Version="1.16.4" />
    <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.21.0" />
    <PackageReference Include="Microsoft.Azure.Functions.Worker.ApplicationInsights" Version="1.1.0" />
    <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.DurableTask" Version="2.13.1" />
  </ItemGroup>
```



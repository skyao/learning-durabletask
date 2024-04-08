---
title: "运行单元测试的准备工作"
linkTitle: "准备工作"
weight: 10
date: 2024-01-19
description: >
  运行 Durable Task 单元测试的准备工作
---





### 运行单元测试的准备工作

https://github.com/Azure/durabletask#development-notes



To run unit tests, you must specify your Service Bus connection string for the tests to use. You can do this via the **ServiceBusConnectionString** app.config value in the test project, or by defining a **DurableTaskTestServiceBusConnectionString** environment variable. The benefit of the environment variable is that no temporary source changes are required.

要运行单元测试，您必须指定  Service Bus 连接字符串供测试使用。您可以通过测试项目中的 **ServiceBusConnectionString** app.config值，或通过定义 **DurableTaskTestServiceBusConnectionString** 环境变量来做到这一点。使用环境变量的好处是无需临时更改源代码。

Unit tests also require [Azure Storage Emulator](https://docs.microsoft.com/azure/storage/common/storage-use-emulator), so make sure it's installed and running.

单元测试还需要 [Azure Storage Emulator](https://docs.microsoft.com/azure/storage/common/storage-use-emulator)，因此请确保它已安装并正在运行。

> Note: While it's possible to use in tests a real Azure Storage account it is not recommended to do so because many tests will fail with a 409 Conflict error. This is because tests delete and quickly recreate the same storage tables, and Azure Storage doesn't do well in these conditions. If you really want to change Azure Storage connection string you can do so via the **StorageConnectionString** app.config value in the test project, or by defining a **DurableTaskTestStorageConnectionString** environment variable.

> 注意：虽然可以在测试中使用真实的 Azure Storage 帐户，但不建议这样做，因为许多测试会因 409 Conflict 错误而失败。这是因为测试会删除并快速重新创建相同的存储表，而 Azure Storage 在这种情况下表现不佳。如果真的要更改 Azure Storage 连接字符串，可以通过测试项目中的 **StorageConnectionString** app.config 值，或通过定义 **DurableTaskTestStorageConnectionString** 环境变量来实现。



### 修改

StorageConnectionString 有在两个地方进行定义，默认值都是空

- Test\DurableTask.Core.Tests\app.config
- Test\DurableTask.ServiceBus.Tests\app.config

还是定义环境变量会更合适

```bash
export DurableTaskTestStorageConnectionString=""
```



### Azure Storage Emulator 

使用文档：[使用 Azure 存储模拟器进行开发和测试（已弃用）](https://learn.microsoft.com/zh-cn/azure/storage/common/storage-use-emulator)

下载并安装

https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409 

若要启动 Azure 存储模拟器：

1. 选择“开始”按钮或按“Windows”键。
2. 开始键入 `Azure Storage Emulator`。
3. 从所示应用程序的列表中选择该模拟器。



### Azurite



在 Visual Studio Code 中，选择“扩展”图标并搜索“Azurite”。 选择“安装”按钮以安装 Azurite 扩展。








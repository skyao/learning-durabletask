---
title: "持久性存储概述"
linkTitle: "概述"
weight: 1
date: 2024-01-19
description: >
  Durable Task 的后端持久性存储概述
---



### 介绍

https://github.com/Azure/durabletask#supported-persistance-stores

支持的持久性存储

从 v2.x 版开始，持久任务框架支持一组可扩展的后端持久性存储。每个存储都可以使用不同的 NuGet 软件包启用。所有软件包的最新版本都已签名，可在 nuget.org 上下载。

| Package                        | 详细信息                                                     | 发展状况                   |
| ------------------------------ | ------------------------------------------------------------ | -------------------------- |
| DurableTask.ServiceBus         | 协调消息和运行时状态存储在 Service Bus 队列中，而跟踪状态存储在 Azure Storage 中。该提供商的优势在于其成熟性和事务一致性。不过，微软已不再对其进行积极开发。 | 生产准备就绪，但未积极维护 |
| DurableTask.AzureStorage       | 所有协调状态都存储在  Azure Storage queues, tables, 和 blobs 中。该提供商的优势在于服务依赖性最小、效率高、功能丰富。这是唯一可用于 [Durable Functions](https://docs.microsoft.com/azure/azure-functions/durable/) 的后端。 | 生产准备就绪并积极维护     |
| DurableTask.AzureServiceFabric | 所有协调状态都存储在 [Azure Service Fabric Reliable Collections](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections) 中。如果您在 [Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) 中托管应用程序，并且不想在存储状态时依赖外部资源，那么这是一个理想的选择。 | 生产准备就绪并积极维护     |
| DurableTask.Netherite          | 由微软研究院开发的超高性能后端，使用微软研究院的 [FASTER](https://www.microsoft.com/research/project/faster/) 数据库技术将状态存储在 [Azure Event Hubs](https://azure.microsoft.com/en-us/services/event-hubs/) 和 Azure Page Blobs 中。[GitHub Repo](https://github.com/microsoft/durabletask-netherite) | 生产准备就绪并积极维护     |
| DurableTask.SqlServer          | 所有协调状态都存储在 Microsoft SQL Server 或 Azure SQL 数据库中，并带有索引表和存储过程，以便直接交互。 [👉 GitHub Repo](https://github.com/microsoft/durabletask-mssql) | 生产准备就绪并积极维护     |
| DurableTask.Emulator           | 这是一个内存存储，仅供测试之用。不建议用于任何生产工作负载。 | 未进行积极维护             |

持久任务框架的核心编程模型包含在 [DurableTask.Core](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Core/) 软件包中，该软件包也在积极开发中。



### 代码实现



| Package                        | 对应代码实现                                                 |
| ------------------------------ | ------------------------------------------------------------ |
| DurableTask.ServiceBus         | src\DurableTask.ServiceBus                                   |
| DurableTask.AzureStorage       | src\DurableTask.AzureStorage                                 |
| DurableTask.AzureServiceFabric | src\DurableTask.AzureServiceFabric                           |
| DurableTask.Netherite          | https://github.com/microsoft/durabletask-netherite           |
| DurableTask.SqlServer          | src\DurableTask.SqlServer<br />https://github.com/microsoft/durabletask-mssql |
| DurableTask.Emulator           | src\DurableTask.Emulator                                     |


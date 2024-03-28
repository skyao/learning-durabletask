---
title: "DTFx概述"
linkTitle: "概述"
weight: 10
date: 2024-01-19
description: >
  DTFx概述
---

## DTFx github首页的介绍

> https://github.com/Azure/durabletask

持久任务框架（DTFx）是一个类库，允许用户使用简单的 async/await 编码结构在 C# 中编写长期运行的持久工作流（称为 *orchestrations*）。微软的各个团队都大量使用它来可靠地协调长期运行的配置、监控和管理操作。只需添加更多的工作机器，就可以线性地扩展协调。该框架还用于支持[Azure Functions](https://azure.microsoft.com/services/functions/)的无服务器[Durable Functions](https://docs.microsoft.com/azure/azure-functions/durable/durable-functions-overview)扩展。

通过开源这个项目，我们希望为社区提供一个极具成本效益的工作流系统替代方案。我们还希望围绕这个简单但功能强大的框架，建立一个由供应商和活动组成的生态系统。


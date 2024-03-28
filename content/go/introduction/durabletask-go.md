---
title: "DurableTask概述"
linkTitle: "概述"
weight: 10
date: 2024-01-19
description: >
  DurableTask概述
---

## durabletask-go github首页的介绍 

> https://github.com/microsoft/durabletask-go

持久任务框架（Durable Task Framework）是一个轻量级、可嵌入的引擎，用于将持久、容错的业务逻辑（协调）编写成普通代码。该引擎本身是用 Go 语言编写的，旨在嵌入到其他基于 Go 的进程中。它暴露了一个 gRPC 端点，支持用任何语言编写持久流。目前，.NET 和 Java 的 SDK 都能使用该 gRPC 端点，未来还会有更多。也可以直接用 Go 编写协调，并在本地进程中运行。

该项目在很大程度上是对[基于.NET 的持久任务框架](https://github.com/Azure/durabletask) 的 Go 克隆，微软的多个云服务团队都使用该框架来构建可靠的控制平面和管理基础架构。它还从[Go Workflows](https://github.com/cschleiden/go-workflows)项目中汲取了灵感，而[Go Workflows](https://github.com/cschleiden/go-workflows)项目本身也是一个 Go 项目，大量借鉴了 Durable Task Framework 和[Temporal](https://github.com/temporalio/temporal)。主要区别在于 Durable Task 引擎设计用于 sidecar 架构。
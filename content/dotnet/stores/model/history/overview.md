---
title: "History概述"
linkTitle: "概述"
weight: 1
date: 2024-01-19
description: >
  核心编程模型的 History event 概述
---



### 介绍

> 以下介绍来自 README.md

Durable Task Framework History Events

以下是构成协调状态的一些常见历史事件。您可以在 DTFx 的 [Azure Storage](https://github.com/Azure/durabletask/tree/main/src/DurableTask.AzureStorage) 和 [MSSQL](https://github.com/microsoft/durabletask-mssql) 存储后端的历史记录表中轻松查看这些事件。在使用 DTFx 代码、调试问题或创建直接读取历史记录的诊断工具（如 [Durable Functions Monitor 项目](https://github.com/scale-tone/DurableFunctionsMonitor)）时，了解这些事件非常有用。

| Event Type | Description |
|-|-|
| `OrchestratorStarted` | 协调器函数正在开始新的_执行/execution_。您将在历史记录中看到许多此类事件--每次协调器从 "等待 "状态恢复时都会出现一个。请注意，这并不意味着协调器首次启动--首次执行由 "ExecutionStarted "历史事件表示（见下文）。该事件的 timestamp 时间戳用于填充 `CurrentDateTimeUtc` 属性。 |
| `ExecutionStarted` | 协调已开始首次执行。该事件包含协调器名称、输入内容和协调器的_scheduled_时间（可能早于历史记录中前面的 `OrchestratorStarted`事件）。这总是协调历史中的第二个事件。 |
| `TaskScheduled` | 协调器调度了一项活动任务。该事件包括活动名称、输入和一个连续的 "EventId"，可用于将 "TaskScheduled " 事件与相应的 "TaskCompleted "或 "TaskFailed "事件关联起来。请注意，如果一个活动任务被重试，可能会生成多个 `Task***` 事件。 |
| `TaskCompleted` | 调度的任务活动已成功完成。`TaskScheduledId` 字段将与相应 `TaskScheduled` 事件的 "EventId" 字段匹配。 |
| `TaskFailed` | 计划的任务活动以失败告终。TaskScheduledId 字段将与相应 "TaskScheduled" 事件的 "EventId" 字段匹配。 |
| `SubOrchestrationInstanceCreated` | 协调器已调度子协调器。该事件包含已调度协调器的名称、实例 ID、输入和有序事件 ID，可用于将 SubOrchestrationInstanceCreated 事件与后续的 SubOrchestrationInstanceCompleted 或 SubOrchestrationInstanceFailed 历史事件关联起来。时间戳指的是调度子协调器的时间，它将早于开始执行的时间。请注意，如果一个活动任务被重试，可能会产生多个 SubOrchestrationInstance*** 事件。 |
| `SubOrchestrationInstanceCompleted` | 调度的子协调器已成功完成。TaskScheduledId "字段将与相应 "SubOrchestrationInstanceCreated "事件的 "EventId "字段匹配。 |
| `SubOrchestrationInstanceFailed` | 计划的子协调器已完成，但出现故障。TaskScheduledId 字段将与相应 SubOrchestrationInstanceCreated 事件的 EventId 字段匹配。 |
| `TimerCreated` | 协调器安排了一个持久定时器。FireAt "属性包含定时器启动的日期。 |
| `TimerFired` | 先前安排的持久定时器已启动。TimerId 字段将与相应 TimeCreated 事件的 EventId 字段匹配。 |
| `EventRaised` | 协调（或[持久实体](https://docs.microsoft.com/azure/azure-functions/durable/durable-functions-entities)中的实体）收到外部事件。该记录包含事件名称、有效载荷和事件_发送_的时间戳（应与历史事件实际被持久化的时间相同或更早）。 |
| `EventSent` | 协调（或entity）向另一个协调（或entity）发送了单向消息。 |
| `ExecutionCompleted` | 协调已完成。该事件包括协调的输出，不区分成功或失败。 |
| `ExecutionTerminated` | 协调被 API 调用强制终止。该事件的时间戳表示计划终止的时间，而不一定是实际终止的时间。 |
| `OrchestratorCompleted` | 协调器函数已等待并提交了任何副作用。您将在历史记录中看到许多此类事件--协调器每次等待时都会出现一个。请注意，这并不意味着协调器已经完成（完成由 `ExecutionCompleted` 或 `ExecutionTerminated` 表示）。 |
| `GenericEvent` | 通用历史事件，有一个 `Data` 字段，但没有特定含义。这种历史事件并不常用。在某些情况下，该事件用于触发空闲协调的全新重放，例如在协调重绕之后。 |
| `HistoryStateEvent` | 包含协调历史快照的历史事件。大多数现代后端类型都不使用这种事件类型。 |

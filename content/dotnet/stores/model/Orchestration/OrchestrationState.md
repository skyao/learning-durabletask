---
title: "OrchestrationState"
linkTitle: "OrchestrationState"
weight: 30
date: 2024-01-19
description: >
  核心编程模型之 OrchestrationState
---



`src\DurableTask.Core\OrchestrationState.cs`

OrchestrationState 中定义了几个属性：

- CompletedTime
- CompressedSize
- CreatedTime
- Input
- LastUpdatedTime
- Name
- OrchestrationInstance: 包含 InstanceId 和 ExecutionId
- Output
- ParentInstance
- Size
- Status
- Tags
- Version: string  格式，看能否复用。
- Generation
- ScheduledStartTime
- FailureDetails




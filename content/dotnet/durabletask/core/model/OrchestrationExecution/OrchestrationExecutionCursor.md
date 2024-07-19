---
title: "OrchestrationExecutionCursor"
linkTitle: "OrchestrationExecutionCursor"
weight: 20
date: 2024-01-19
description: >
  OrchestrationExecutionCursor
---



`src\DurableTask.Core\OrchestrationExecutionCursor.cs`

*Context associated with the orchestration being executed.*

与正在执行的协调相关的上下文。

类定义：

```c#
    internal class OrchestrationExecutionCursor
    {
    }
```

构造函数：

```c#
        public OrchestrationExecutionCursor(
            OrchestrationRuntimeState state,
            TaskOrchestration orchestration,
            TaskOrchestrationExecutor executor,
            IEnumerable<OrchestratorAction> latestDecisions)
        {
            RuntimeState = state;
            TaskOrchestration = orchestration;
            OrchestrationExecutor = executor;
            LatestDecisions = latestDecisions;
        }
```

get/set 方法：

```c#
        public OrchestrationRuntimeState RuntimeState { get; }

        public TaskOrchestration TaskOrchestration { get; }

        public TaskOrchestrationExecutor OrchestrationExecutor { get; }

        public IEnumerable<OrchestratorAction> LatestDecisions { get; set; }
```

就是一个值对象。

Cursor 游标体现在哪里？

---
title: "quickstart事件顺序"
linkTitle: "事件顺序"
weight: 40
date: 2024-01-19
description: >
  分析durabletask Quickstart中事件出现和处理的顺序
---



|        | Orchestration <br />Fetch | Activity <br />Fetch | 执行用户代码           |
| ------ | ------------------------- | -------------------- | ---------------------- |
|        | Start                     | Start                |                        |
| 第一轮 |                           |                      |                        |
|        | StartFetch                | StartFetch           |                        |
|        | ---->> ExecutionStarted   |                      |                        |
|        | EndFetch                  |                      |                        |
|        | StartFetch                |                      |                        |
|        |                           |                      | GreetingsOrchestration |
|        |                           | -->. ??              |                        |
|        |                           | EndFetch             |                        |
|        |                           | StartFetch           |                        |
|        |                           |                      | GetUserTask            |
| 第二轮 |                           |                      |                        |
|        | ---->> TaskCompleted      |                      |                        |
|        | EndFetch                  |                      |                        |
|        | StartFetch                |                      |                        |
|        |                           |                      | SendGreetingTask       |
| 第三轮 |                           |                      |                        |
|        |                           |                      |                        |
|        | EndFetch                  |                      |                        |
|        |                           |                      |                        |



### LockNextTaskOrchestrationWorkItemAsync



LockNextTaskOrchestrationWorkItemAsync 方法被调用的这个日志出现过三次：

```bash
********* versioning  ************* LockNextTaskOrchestrationWorkItemAsync()
```

第一次被调用时，event history 列表有：

- OrchestratorStartedEvent (new)
- ExecutionStartedEvent (new)

对应日志：

```bash
ProcessEvent: EventType=ExecutionStarted
GreetingsOrchestration.RunTask called
```

第二次被调用时，event history 列表有：

- OrchestratorStartedEvent 
- ExecutionStartedEvent
- TaskScheduledEvent
- OrchestratorCompletedEvent
- OrchestratorStartedEvent  (new)
- TaskCompletedEvent  (new)

对应日志：

```bash
ProcessEvent: EventType=ExecutionStarted
GreetingsOrchestration.RunTask called
ProcessEvent: EventType=TaskScheduled
ProcessEvent: EventType=OrchestratorCompleted
ProcessEvent: EventType=TaskCompleted
```

第三次被调用时，event history 列表有：

- OrchestratorStartedEvent 
- ExecutionStartedEvent
- TaskScheduledEvent
- OrchestratorCompletedEvent
- OrchestratorStartedEvent 
- TaskCompletedEvent
- TaskScheduledEvent
- OrchestratorCompletedEvent
- OrchestratorStartedEvent  (new)
- TaskCompletedEvent   (new)

对应日志：

```bash
ProcessEvent: EventType=ExecutionStarted
GreetingsOrchestration.RunTask called
ProcessEvent: EventType=TaskScheduled
ProcessEvent: EventType=OrchestratorCompleted
ProcessEvent: EventType=TaskCompleted
ProcessEvent: EventType=TaskScheduled
ProcessEvent: EventType=OrchestratorCompleted
ProcessEvent: EventType=TaskCompleted
```






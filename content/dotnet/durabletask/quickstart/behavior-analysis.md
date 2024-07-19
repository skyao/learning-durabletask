---
title: "quickstart行为分析"
linkTitle: "行为分析"
weight: 30
date: 2024-01-19
description: >
  分析durabletask Quickstart的行为
---



命令开始执行：

```bash
$ dotnet run -c debug -s Greetings

Start Orchestration: Greetings
```

## start workflow instance

Workflow Instance 开始，生成 InstanceId 和 ExecutionId：

```bash
Workflow Instance Started: [InstanceId: 16af7b96-9409-4618-b33a-e7cc3a121a1a, ExecutionId: 410f7f47fdd9444ab3fb3ce6d00e9649]
```

然后产生了两个 Event （为什么 EventId 都是 3？）TaskOrchestrationDispatcher 和 TaskActivityDispatcher 启动，eventType 都是 WorkItemDispatcherStart：

```bash
EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : WorkItemDispatcher('TaskOrchestrationDispatcher') starting. Id e0bc782c9aa84a0a8a256cf7a5d4e06e.] [info : ] [eventType : WorkItemDispatcherStart] , EventName : InfoInfo, Timestamp : 2024-04-28T01:50:56.3468829Z, ProcessId : 6536, ThreadId : 14068

EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : WorkItemDispatcher('TaskActivityDispatcher') starting. Id 38b3e9add82043708a8643b81cf2f643.] [info : ] [eventType : WorkItemDispatcherStart] , EventName : InfoInfo, Timestamp : 2024-04-28T01:50:56.3625156Z, ProcessId : 6536, ThreadId : 14068
```

超时时间为 60 秒：

```bash
Waiting up to 60 seconds for completion.
```

TaskOrchestrationDispatcher 和 TaskActivityDispatcher 开始 fetch：

```bash
EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskOrchestrationDispatcher-e0bc782c9aa84a0a8a256cf7a5d4e06e-0: Starting fetch with timeout of 00:00:30 (0/100 max)] [info : ] [eventType : WorkItemDispatcherDispatch-StartFetch] , EventName : TraceInfo, Timestamp : 2024-04-28T01:50:56.3625156Z, ProcessId : 6536, ThreadId : 12032

EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskActivityDispatcher-38b3e9add82043708a8643b81cf2f643-0: Starting fetch with timeout of 00:00:30 (0/10 max)] [info : ] [eventType : WorkItemDispatcherDispatch-StartFetch] , EventName : TraceInfo, Timestamp : 2024-04-28T01:50:56.3625156Z, ProcessId : 6536, ThreadId : 9632
```

这是我额外增加的日志，LockNextTaskOrchestrationWorkItemAsync 方法被调用了：

```bash
********* versioning  ************* LockNextTaskOrchestrationWorkItemAsync()
```

## Orchestration第一轮

### ExecutionStarted

TaskOrchestrationDispatcher 成功 fetch 到一个 workitem，然后又继续去 fetch 下一个：

```bash
EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskOrchestrationDispatcher-e0bc782c9aa84a0a8a256cf7a5d4e06e-0: After fetch (2961 ms) (0/100 max)] [info : ] [eventType : WorkItemDispatcherDispatch-EndFetch] , EventName : TraceInfo, Timestamp : 2024-04-28T01:50:59.3155825Z, ProcessId : 6536, ThreadId : 12032, ActivityId : d85171a0-5ef1-4e78-a32b-0d31cb396de7

EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskOrchestrationDispatcher-e0bc782c9aa84a0a8a256cf7a5d4e06e-0: Starting fetch with timeout of 00:00:30 (1/100 max)] [info : ] [eventType : WorkItemDispatcherDispatch-StartFetch] , EventName : TraceInfo, Timestamp : 2024-04-28T01:50:59.3155825Z, ProcessId : 6536, ThreadId : 12032, ActivityId : d85171a0-5ef1-4e78-a32b-0d31cb396de7
```

TaskOrchestrationDispatcher 开始处理刚才 fetch 到的 workItem

```bash
EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskOrchestrationDispatcher-e0bc782c9aa84a0a8a256cf7a5d4e06e-0: Starting to process workItem 16af7b96-9409-4618-b33a-e7cc3a121a1a] [info : ] [eventType : WorkItemDispatcherProcess-Begin] , EventName : InfoInfo, Timestamp : 2024-04-28T01:50:59.3155825Z, ProcessId : 6536, ThreadId : 12032, ActivityId : d85171a0-5ef1-4e78-a32b-0d31cb396de7

```

TaskOrchestrationDispatcher 开始处理 ExecutionStarted Event：

```bash
EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : 16af7b96-9409-4618-b33a-e7cc3a121a1a] [executionId : 410f7f47fdd9444ab3fb3ce6d00e9649] [sessionId : ] [message : Processing new event with Id -1 and type ExecutionStarted] [info : ] [eventType : TaskOrchestrationDispatcher-ProcessEvent] , EventName : InfoInfo, Timestamp : 2024-04-28T01:50:59.3312077Z, ProcessId : 6536, ThreadId : 12032, ActivityId : d85171a0-5ef1-4e78-a32b-0d31cb396de7
```

开始执行 user orchestration：

```bash
EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : 16af7b96-9409-4618-b33a-e7cc3a121a1a] [executionId : 410f7f47fdd9444ab3fb3ce6d00e9649] [sessionId : ] [message : Executing user orchestration: 
```

此时有两个 event ( OrchestratorStartedEvent 和 ExecutionStartedEvent), 这两个 event 都是 newEvent：

```json
{
  "$type": "DurableTask.Core.OrchestrationRuntimeStateDump, DurableTask.Core",
  "Events": [
    {
      "$type": "DurableTask.Core.History.OrchestratorStartedEvent, DurableTask.Core",
      "EventType": 12,
      "EventId": -1,
      "IsPlayed": false,
      "Timestamp": "2024-04-28T01:50:59.3312077Z"
    },
    {
      "$type": "DurableTask.Core.History.ExecutionStartedEvent, DurableTask.Core",
      "OrchestrationInstance": null,
      "EventType": 0,
      "ParentInstance": null,
      "Name": null,
      "Version": null,
      "Input": "[..snipped..]",
      "Tags": null,
      "Correlation": null,
      "ParentTraceContext": null,
      "ScheduledStartTime": null,
      "Generation": null,
      "EventId": -1,
      "IsPlayed": false,
      "Timestamp": "2024-04-28T01:50:54.3937812Z"
    }
  ],
  "NewEvents": [
    {
      "$type": "DurableTask.Core.History.OrchestratorStartedEvent, DurableTask.Core",
      "EventType": 12,
      "EventId": -1,
      "IsPlayed": false,
      "Timestamp": "2024-04-28T01:50:59.3312077Z"
    },
    {
      "$type": "DurableTask.Core.History.ExecutionStartedEvent, DurableTask.Core",
      "OrchestrationInstance": null,
      "EventType": 0,
      "ParentInstance": null,
      "Name": null,
      "Version": null,
      "Input": "[..snipped..]",
      "Tags": null,
      "Correlation": null,
      "ParentTraceContext": null,
      "ScheduledStartTime": null,
      "Generation": null,
      "EventId": -1,
      "IsPlayed": false,
      "Timestamp": "2024-04-28T01:50:54.3937812Z"
    }
  ],
  "EventCount": 0,
  "NewEventsCount": 0
}
```

### 执行用户代码：GreetingsOrchestration

开始执行用户的编排代码，

```bash
 [eventType : TaskOrchestrationDispatcher-ExecuteUserOrchestration-Begin] , EventName : TraceInfo, Timestamp : 2024-04-28T01:50:59.3312077Z, ProcessId : 6536, ThreadId : 12032, ActivityId : d85171a0-5ef1-4e78-a32b-0d31cb396de7

ProcessEvent: EventType=ExecutionStarted
GreetingsOrchestration.RunTask called
```

`GreetingsOrchestration.RunTask called` 是我增加的日志，这是 example 中的 Orchestration 的实现代码：

```c#
    public class GreetingsOrchestration : TaskOrchestration<string,string>
    {
        public override async Task<string> RunTask(OrchestrationContext context, string input)
        {
            Console.WriteLine("GreetingsOrchestration.RunTask called");
          
            string user = await context.ScheduleTask<string>(typeof(GetUserTask));
            string greeting = await context.ScheduleTask<string>(typeof(SendGreetingTask), user);
            return greeting;
        }
    }
```

Orchestration 执行完成，`Executed user orchestration` ，然后收到一个 orchestrator action `ScheduleOrchestrator`：

> 但这个有点质疑， task 还没完成了，可能是 await 退出了？？等下翻一下源码： 

```bash
EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : 16af7b96-9409-4618-b33a-e7cc3a121a1a] [executionId : 410f7f47fdd9444ab3fb3ce6d00e9649] [sessionId : ] [message : Executed user orchestration. Received 1 orchestrator actions: 0:ScheduleOrchestrator] [info : ] [eventType : TaskOrchestrationDispatcher-ExecuteUserOrchestration-End] , EventName : InfoInfo, Timestamp : 2024-04-28T01:50:59.3468324Z, ProcessId : 6536, ThreadId : 12032, ActivityId : d85171a0-5ef1-4e78-a32b-0d31cb396de7
```

TaskOrchestrationDispatcher 开始处理  orchestrator action `ScheduleOrchestrator`：

```bash
EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : 16af7b96-9409-4618-b33a-e7cc3a121a1a] [executionId : 410f7f47fdd9444ab3fb3ce6d00e9649] [sessionId : ] [message : Processing orchestrator action of type ScheduleOrchestrator] [info : ] [eventType : TaskOrchestrationDispatcher-ProcessOrchestratorAction] , EventName : InfoInfo, Timestamp : 2024-04-28T01:50:59.3468324Z, ProcessId : 6536, ThreadId : 12032, ActivityId : d85171a0-5ef1-4e78-a32b-0d31cb396de7

OnProcessWorkItemAsync - Processing orchestrator action of type ScheduleOrchestrator
```

`OnProcessWorkItemAsync - Processing orchestrator action of type ScheduleOrchestrator` 是我增加的日志。

### 执行用户代码：GetUserTask

这个时候 TaskActivityDispatcher 终于 fetch 到一个 workitem 了：

```bash
EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskActivityDispatcher-38b3e9add82043708a8643b81cf2f643-0: After fetch (3161 ms) (0/10 max)] [info : ] [eventType : WorkItemDispatcherDispatch-EndFetch] , EventName : TraceInfo, Timestamp : 2024-04-28T01:50:59.5187069Z, ProcessId : 6536, ThreadId : 13976, ActivityId : 1e0c99d8-4905-4149-a790-332669c907af
```

TaskActivityDispatcher 继续 fetch 下一个：

```bash
EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskActivityDispatcher-38b3e9add82043708a8643b81cf2f643-0: Starting fetch with timeout of 00:00:30 (1/10 max)] [info : ] [eventType : WorkItemDispatcherDispatch-StartFetch] , EventName : TraceInfo, Timestamp : 2024-04-28T01:50:59.5187069Z, ProcessId : 6536, ThreadId : 13976, ActivityId : 1e0c99d8-4905-4149-a790-332669c907af
```

TaskActivityDispatcher 开始处理刚才 fetch 到的 work item：

```bash
EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskActivityDispatcher-38b3e9add82043708a8643b81cf2f643-0: Starting to process workItem 585e3de9-e953-4f39-af4f-c4a9b7bcd657] [info : ] [eventType : WorkItemDispatcherProcess-Begin] , EventName : InfoInfo, Timestamp : 2024-04-28T01:50:59.5187069Z, ProcessId : 6536, ThreadId : 12032, ActivityId : d85171a0-5ef1-4e78-a32b-0d31cb396de7
```

这个 sample 里面就两个 task，第一个 task 是 GetUserTask：

```c#
            string user = await context.ScheduleTask<string>(typeof(GetUserTask));
            string greeting = await context.ScheduleTask<string>(typeof(SendGreetingTask), user);
            return greeting;
```

会弹出一个窗口要求输入用户的 name，日志如下：

```bash
Waiting for user to enter name...
```

输入名字 sky 之后，这个 task 执行完成，日志显示 `Finished processing workItem`， 这是 TaskOrchestrationDispatcher 打印的：

```bash
EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskOrchestrationDispatcher-e0bc782c9aa84a0a8a256cf7a5d4e06e-0: Finished processing workItem 16af7b96-9409-4618-b33a-e7cc3a121a1a] [info : ] [eventType : WorkItemDispatcherProcess-End] , EventName : InfoInfo, Timestamp : 2024-04-28T01:50:59.8155822Z, ProcessId : 6536, ThreadId : 13976, ActivityId : 1e0c99d8-4905-4149-a790-332669c907af

User Name Entered: sky
```

TaskActivityDispatcher 又打印了一次 Finished processing workItem 的日志：

```bash
EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskActivityDispatcher-38b3e9add82043708a8643b81cf2f643-0: Finished processing workItem 585e3de9-e953-4f39-af4f-c4a9b7bcd657] [info : ] [eventType : WorkItemDispatcherProcess-End] , EventName : InfoInfo, Timestamp : 2024-04-28T01:51:04.3624392Z, ProcessId : 6536, ThreadId : 11248
```

TaskOrchestrationDispatcher 又一次 fetch 到 workitem：

```bash
********* versioning  ************* LockNextTaskOrchestrationWorkItemAsync()
EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskOrchestrationDispatcher-e0bc782c9aa84a0a8a256cf7a5d4e06e-0: After fetch (5214 ms) (0/100 max)] [info : ] [eventType : WorkItemDispatcherDispatch-EndFetch] , EventName : TraceInfo, Timestamp : 2024-04-28T01:51:04.5343127Z, ProcessId : 6536, ThreadId : 14068, ActivityId : f698e9a6-907c-4092-bd48-e19e7ba6a2a9
```

TaskOrchestrationDispatcher 继续 fetch 下一个 workitem ：

```bash
EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskOrchestrationDispatcher-e0bc782c9aa84a0a8a256cf7a5d4e06e-0: Starting fetch with timeout of 00:00:30 (1/100 max)] [info : ] [eventType : WorkItemDispatcherDispatch-StartFetch] , EventName : TraceInfo, Timestamp : 2024-04-28T01:51:04.5343127Z, ProcessId : 6536, ThreadId : 14068, ActivityId : f698e9a6-907c-4092-bd48-e19e7ba6a2a9
```

TaskOrchestrationDispatcher 开始处理刚才 fetch 到的 workitem：

```bash
EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskOrchestrationDispatcher-e0bc782c9aa84a0a8a256cf7a5d4e06e-0: Starting to process workItem 16af7b96-9409-4618-b33a-e7cc3a121a1a] [info : ] [eventType : WorkItemDispatcherProcess-Begin] , EventName : InfoInfo, Timestamp : 2024-04-28T01:51:04.5343127Z, ProcessId : 6536, ThreadId : 9632, ActivityId : fec9f5fa-cfa6-4115-95f1-7f40e3e9d6aa
```

原来是 TaskCompleted Event：

```bash
EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : 16af7b96-9409-4618-b33a-e7cc3a121a1a] [executionId : 410f7f47fdd9444ab3fb3ce6d00e9649] [sessionId : ] [message : Processing new event with Id -1 and type TaskCompleted] [info : ] [eventType : TaskOrchestrationDispatcher-ProcessEvent] , EventName : InfoInfo, Timestamp : 2024-04-28T01:51:04.5343127Z, ProcessId : 6536, ThreadId : 9632, ActivityId : fec9f5fa-cfa6-4115-95f1-7f40e3e9d6aa
```



```bash
EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : 16af7b96-9409-4618-b33a-e7cc3a121a1a] [executionId : 410f7f47fdd9444ab3fb3ce6d00e9649] [sessionId : ] [message : Executing user orchestration: 
```

user orchestration 除了之前的两个 event（OrchestratorStartedEvent 和 ExecutionStartedEvent）之外，还多了 TaskScheduledEvent（GetUserTask），OrchestratorCompletedEvent ， OrchestratorStartedEvent， TaskCompletedEvent。

```json
{
  "$type": "DurableTask.Core.OrchestrationRuntimeStateDump, DurableTask.Core",
  "Events": [
    {
      "$type": "DurableTask.Core.History.OrchestratorStartedEvent, DurableTask.Core",
      "EventType": 12,
      "EventId": -1,
      "IsPlayed": false,
      "Timestamp": "2024-04-28T01:50:59.3312077Z"
    },
    {
      "$type": "DurableTask.Core.History.ExecutionStartedEvent, DurableTask.Core",
      "OrchestrationInstance": null,
      "EventType": 0,
      "ParentInstance": null,
      "Name": null,
      "Version": null,
      "Input": "[..snipped..]",
      "Tags": null,
      "Correlation": null,
      "ParentTraceContext": null,
      "ScheduledStartTime": null,
      "Generation": null,
      "EventId": -1,
      "IsPlayed": true,
      "Timestamp": "2024-04-28T01:50:54.3937812Z"
    },
    {
      "$type": "DurableTask.Core.History.TaskScheduledEvent, DurableTask.Core",
      "EventType": 4,
      "Name": "DurableTask.Samples.Greetings.GetUserTask",
      "Version": "",
      "Input": "[..snipped..]",
      "ParentTraceContext": null,
      "EventId": 0,
      "IsPlayed": false,
      "Timestamp": "2024-04-28T01:50:59.3468324Z"
    },
    {
      "$type": "DurableTask.Core.History.OrchestratorCompletedEvent, DurableTask.Core",
      "EventType": 13,
      "EventId": -1,
      "IsPlayed": false,
      "Timestamp": "2024-04-28T01:50:59.3468324Z"
    },
    {
      "$type": "DurableTask.Core.History.OrchestratorStartedEvent, DurableTask.Core",
      "EventType": 12,
      "EventId": -1,
      "IsPlayed": false,
      "Timestamp": "2024-04-28T01:51:04.5343127Z"
    },
    {
      "$type": "DurableTask.Core.History.TaskCompletedEvent, DurableTask.Core",
      "EventType": 5,
      "TaskScheduledId": 0,
      "Result": "[..snipped..]",
      "EventId": -1,
      "IsPlayed": false,
      "Timestamp": "2024-04-28T01:51:04.1905617Z"
    }
  ],
  "NewEvents": [
    {
      "$type": "DurableTask.Core.History.OrchestratorStartedEvent, DurableTask.Core",
      "EventType": 12,
      "EventId": -1,
      "IsPlayed": false,
      "Timestamp": "2024-04-28T01:51:04.5343127Z"
    },
    {
      "$type": "DurableTask.Core.History.TaskCompletedEvent, DurableTask.Core",
      "EventType": 5,
      "TaskScheduledId": 0,
      "Result": "[..snipped..]",
      "EventId": -1,
      "IsPlayed": false,
      "Timestamp": "2024-04-28T01:51:04.1905617Z"
    }
  ],
  "EventCount": 0,
  "NewEventsCount": 0
}
```

其中有一个 OrchestratorStartedEvent 是新的，还有 TaskCompletedEvent。

## Orchestration第二轮

TaskOrchestrationDispatcher 再次 Execute User Orchestration, 这里我增加了日志打印，把 ProcessEvent 的 evnet 都打印了出来：

```bash
[info : ] [eventType : TaskOrchestrationDispatcher-ExecuteUserOrchestration-Begin] , EventName : TraceInfo, Timestamp : 2024-04-28T01:51:04.5343127Z, ProcessId : 6536, ThreadId : 9632, ActivityId : fec9f5fa-cfa6-4115-95f1-7f40e3e9d6aa

ProcessEvent: EventType=ExecutionStarted
GreetingsOrchestration.RunTask called
ProcessEvent: EventType=TaskScheduled
ProcessEvent: EventType=OrchestratorCompleted
ProcessEvent: EventType=TaskCompleted
```

Executed user orchestration 执行完之后，又收到了一个 orchestrator action `ScheduleOrchestrator`：

```bash
EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : 16af7b96-9409-4618-b33a-e7cc3a121a1a] [executionId : 410f7f47fdd9444ab3fb3ce6d00e9649] [sessionId : ] [message : Executed user orchestration. Received 1 orchestrator actions: 1:ScheduleOrchestrator] [info : ] [eventType : TaskOrchestrationDispatcher-ExecuteUserOrchestration-End] , EventName : InfoInfo, Timestamp : 2024-04-28T01:51:04.5343127Z, ProcessId : 6536, ThreadId : 9632, ActivityId : fec9f5fa-cfa6-4115-95f1-7f40e3e9d6aa
```

TaskOrchestrationDispatcher 开始执行 ScheduleOrchestrator 这个 orchestrator action：

```bash
EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : 16af7b96-9409-4618-b33a-e7cc3a121a1a] [executionId : 410f7f47fdd9444ab3fb3ce6d00e9649] [sessionId : ] [message : Processing orchestrator action of type ScheduleOrchestrator] [info : ] [eventType : TaskOrchestrationDispatcher-ProcessOrchestratorAction] , EventName : InfoInfo, Timestamp : 2024-04-28T01:51:04.5343127Z, ProcessId : 6536, ThreadId : 9632, ActivityId : fec9f5fa-cfa6-4115-95f1-7f40e3e9d6aa

OnProcessWorkItemAsync - Processing orchestrator action of type ScheduleOrchestrator
```

### 执行用户代码：SendGreetingTask

TaskActivityDispatcher 又一次 fetch 到 workitem：

```bash
EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskActivityDispatcher-38b3e9add82043708a8643b81cf2f643-0: After fetch (5183 ms) (0/10 max)] [info : ] [eventType : WorkItemDispatcherDispatch-EndFetch] , EventName : TraceInfo, Timestamp : 2024-04-28T01:51:04.7061869Z, ProcessId : 6536, ThreadId : 11248, ActivityId : 413409db-6e47-413a-b78d-319a802d563c
```

TaskActivityDispatcher 继续 fetch 下一个

```bash
EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskActivityDispatcher-38b3e9add82043708a8643b81cf2f643-0: Starting fetch with timeout of 00:00:30 (1/10 max)] [info : ] [eventType : WorkItemDispatcherDispatch-StartFetch] , EventName : TraceInfo, Timestamp : 2024-04-28T01:51:04.7061869Z, ProcessId : 6536, ThreadId : 11248, ActivityId : 413409db-6e47-413a-b78d-319a802d563c
```

TaskActivityDispatcher 开始处理刚才 fetch 到的 work item

```bash
EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskActivityDispatcher-38b3e9add82043708a8643b81cf2f643-0: Starting to process workItem d9bb55c6-f35e-495b-994e-b6bd106372b1] [info : ] [eventType : WorkItemDispatcherProcess-Begin] , EventName : InfoInfo, Timestamp : 2024-04-28T01:51:04.7061869Z, ProcessId : 6536, ThreadId : 9632, ActivityId : f698e9a6-907c-4092-bd48-e19e7ba6a2a9
```

也就是执行第二个task，SendGreetingTask，表现为打印如下日志：

```bash
Sending greetings to user: sky...
EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskOrchestrationDispatcher-e0bc782c9aa84a0a8a256cf7a5d4e06e-0: Finished processing workItem 16af7b96-9409-4618-b33a-e7cc3a121a1a] [info : ] [eventType : WorkItemDispatcherProcess-End] , EventName : InfoInfo, Timestamp : 2024-04-28T01:51:04.9093117Z, ProcessId : 6536, ThreadId : 13976, ActivityId : f698e9a6-907c-4092-bd48-e19e7ba6a2a9

Greeting sent to sky
```

在两个日志打印 `Sending greetings to user: sky...`   和 `Sending greetings to user: sky...` 之间，有一个 `Thread.Sleep(5 * 1000); :`

```c#
public sealed class SendGreetingTask : TaskActivity<string, string>
    {
        protected override string Execute(DurableTask.Core.TaskContext context, string user)
        {
            string message;
            if (!string.IsNullOrWhiteSpace(user) && user.Equals("TimedOut"))
            {
                message = "GetUser Timed out!!!";
                Console.WriteLine(message);
            }
            else
            {
                Console.WriteLine("Sending greetings to user: " + user + "...");

                Thread.Sleep(5 * 1000);

                message = "Greeting sent to " + user;
                Console.WriteLine(message);
            }

            return message;
        }
    }
```

> TBD: 这个 sleep 有问题，按说不能直接这样 sleep 的。

TaskActivityDispatcher 完成第二个 work item （SendGreetingTask） 的处理

```bash
EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskActivityDispatcher-38b3e9add82043708a8643b81cf2f643-0: Finished processing workItem d9bb55c6-f35e-495b-994e-b6bd106372b1] [info : ] [eventType : WorkItemDispatcherProcess-End] , EventName : InfoInfo, Timestamp : 2024-04-28T01:51:09.8780623Z, ProcessId : 6536, ThreadId : 10076, ActivityId : 1946c0da-30f0-4767-9aff-a5ff1e2bb884
```



TaskOrchestrationDispatcher 继续 fetch 到下一个 work item

```bash
********* versioning  ************* LockNextTaskOrchestrationWorkItemAsync()
EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskOrchestrationDispatcher-e0bc782c9aa84a0a8a256cf7a5d4e06e-0: After fetch (5515 ms) (0/100 max)] [info : ] [eventType : WorkItemDispatcherDispatch-EndFetch] , EventName : TraceInfo, Timestamp : 2024-04-28T01:51:10.0499421Z, ProcessId : 6536, ThreadId : 12032, ActivityId : 4d43f051-56be-45ca-912c-7043390e8cd3
```

TaskOrchestrationDispatcher 继续 fetch 下一个 work item:

```bash
EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskOrchestrationDispatcher-e0bc782c9aa84a0a8a256cf7a5d4e06e-0: Starting fetch with timeout of 00:00:30 (1/100 max)] [info : ] [eventType : WorkItemDispatcherDispatch-StartFetch] , EventName : TraceInfo, Timestamp : 2024-04-28T01:51:10.0499421Z, ProcessId : 6536, ThreadId : 12032, ActivityId : 4d43f051-56be-45ca-912c-7043390e8cd3
```

TaskOrchestrationDispatcher 开始处理 fetch 到的 work item：

```bash
EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskOrchestrationDispatcher-e0bc782c9aa84a0a8a256cf7a5d4e06e-0: Starting to process workItem 16af7b96-9409-4618-b33a-e7cc3a121a1a] [info : ] [eventType : WorkItemDispatcherProcess-Begin] , EventName : InfoInfo, Timestamp : 2024-04-28T01:51:10.0499421Z, ProcessId : 6536, ThreadId : 12032, ActivityId : 4d43f051-56be-45ca-912c-7043390e8cd3
```

TaskOrchestrationDispatcher 处理 TaskCompleted event

```bash
EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : 16af7b96-9409-4618-b33a-e7cc3a121a1a] [executionId : 410f7f47fdd9444ab3fb3ce6d00e9649] [sessionId : ] [message : Processing new event with Id -1 and type TaskCompleted] [info : ] [eventType : TaskOrchestrationDispatcher-ProcessEvent] , EventName : InfoInfo, Timestamp : 2024-04-28T01:51:10.0499421Z, ProcessId : 6536, ThreadId : 12032, ActivityId : 4d43f051-56be-45ca-912c-7043390e8cd3
```



## Orchestration第三轮



```bash
EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : 16af7b96-9409-4618-b33a-e7cc3a121a1a] [executionId : 410f7f47fdd9444ab3fb3ce6d00e9649] [sessionId : ] [message : Executing user orchestration: 
```



```bash
{
  "$type": "DurableTask.Core.OrchestrationRuntimeStateDump, DurableTask.Core",
  "Events": [
    {
      "$type": "DurableTask.Core.History.OrchestratorStartedEvent, DurableTask.Core",
      "EventType": 12,
      "EventId": -1,
      "IsPlayed": false,
      "Timestamp": "2024-04-28T01:50:59.3312077Z"
    },
    {
      "$type": "DurableTask.Core.History.ExecutionStartedEvent, DurableTask.Core",
      "OrchestrationInstance": null,
      "EventType": 0,
      "ParentInstance": null,
      "Name": null,
      "Version": null,
      "Input": "[..snipped..]",
      "Tags": null,
      "Correlation": null,
      "ParentTraceContext": null,
      "ScheduledStartTime": null,
      "Generation": null,
      "EventId": -1,
      "IsPlayed": true,
      "Timestamp": "2024-04-28T01:50:54.3937812Z"
    },
    {
      "$type": "DurableTask.Core.History.TaskScheduledEvent, DurableTask.Core",
      "EventType": 4,
      "Name": "DurableTask.Samples.Greetings.GetUserTask",
      "Version": "",
      "Input": "[..snipped..]",
      "ParentTraceContext": null,
      "EventId": 0,
      "IsPlayed": false,
      "Timestamp": "2024-04-28T01:50:59.3468324Z"
    },
    {
      "$type": "DurableTask.Core.History.OrchestratorCompletedEvent, DurableTask.Core",
      "EventType": 13,
      "EventId": -1,
      "IsPlayed": false,
      "Timestamp": "2024-04-28T01:50:59.3468324Z"
    },
    {
      "$type": "DurableTask.Core.History.OrchestratorStartedEvent, DurableTask.Core",
      "EventType": 12,
      "EventId": -1,
      "IsPlayed": false,
      "Timestamp": "2024-04-28T01:51:04.5343127Z"
    },
    {
      "$type": "DurableTask.Core.History.TaskCompletedEvent, DurableTask.Core",
      "EventType": 5,
      "TaskScheduledId": 0,
      "Result": "[..snipped..]",
      "EventId": -1,
      "IsPlayed": true,
      "Timestamp": "2024-04-28T01:51:04.1905617Z"
    },
    {
      "$type": "DurableTask.Core.History.TaskScheduledEvent, DurableTask.Core",
      "EventType": 4,
      "Name": "DurableTask.Samples.Greetings.SendGreetingTask",
      "Version": "",
      "Input": "[..snipped..]",
      "ParentTraceContext": null,
      "EventId": 1,
      "IsPlayed": false,
      "Timestamp": "2024-04-28T01:51:04.5343127Z"
    },
    {
      "$type": "DurableTask.Core.History.OrchestratorCompletedEvent, DurableTask.Core",
      "EventType": 13,
      "EventId": -1,
      "IsPlayed": false,
      "Timestamp": "2024-04-28T01:51:04.5343127Z"
    },
    {
      "$type": "DurableTask.Core.History.OrchestratorStartedEvent, DurableTask.Core",
      "EventType": 12,
      "EventId": -1,
      "IsPlayed": false,
      "Timestamp": "2024-04-28T01:51:10.0499421Z"
    },
    {
      "$type": "DurableTask.Core.History.TaskCompletedEvent, DurableTask.Core",
      "EventType": 5,
      "TaskScheduledId": 1,
      "Result": "[..snipped..]",
      "EventId": -1,
      "IsPlayed": false,
      "Timestamp": "2024-04-28T01:51:09.7222239Z"
    }
  ],
  "NewEvents": [
    {
      "$type": "DurableTask.Core.History.OrchestratorStartedEvent, DurableTask.Core",
      "EventType": 12,
      "EventId": -1,
      "IsPlayed": false,
      "Timestamp": "2024-04-28T01:51:10.0499421Z"
    },
    {
      "$type": "DurableTask.Core.History.TaskCompletedEvent, DurableTask.Core",
      "EventType": 5,
      "TaskScheduledId": 1,
      "Result": "[..snipped..]",
      "EventId": -1,
      "IsPlayed": false,
      "Timestamp": "2024-04-28T01:51:09.7222239Z"
    }
  ],
  "EventCount": 0,
  "NewEventsCount": 0
}
```



```bash
 [eventType : TaskOrchestrationDispatcher-ExecuteUserOrchestration-Begin] , EventName : TraceInfo, Timestamp : 2024-04-28T01:51:10.0499421Z, ProcessId : 6536, ThreadId : 12032, ActivityId : 4d43f051-56be-45ca-912c-7043390e8cd3

ProcessEvent: EventType=ExecutionStarted
GreetingsOrchestration.RunTask called
ProcessEvent: EventType=TaskScheduled
ProcessEvent: EventType=OrchestratorCompleted
ProcessEvent: EventType=TaskCompleted
ProcessEvent: EventType=TaskScheduled
ProcessEvent: EventType=OrchestratorCompleted
ProcessEvent: EventType=TaskCompleted
EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : 16af7b96-9409-4618-b33a-e7cc3a121a1a] [executionId : 410f7f47fdd9444ab3fb3ce6d00e9649] [sessionId : ] [message : Executed user orchestration. Received 1 orchestrator actions: 2:OrchestrationComplete] [info : ] [eventType : TaskOrchestrationDispatcher-ExecuteUserOrchestration-End] , EventName : InfoInfo, Timestamp : 2024-04-28T01:51:10.0499421Z, ProcessId : 6536, ThreadId : 12032, ActivityId : 4d43f051-56be-45ca-912c-7043390e8cd3

EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : 16af7b96-9409-4618-b33a-e7cc3a121a1a] [executionId : 410f7f47fdd9444ab3fb3ce6d00e9649] [sessionId : ] [message : Processing orchestrator action of type OrchestrationComplete] [info : ] [eventType : TaskOrchestrationDispatcher-ProcessOrchestratorAction] , EventName : InfoInfo, Timestamp : 2024-04-28T01:51:10.0499421Z, ProcessId : 6536, ThreadId : 12032, ActivityId : 4d43f051-56be-45ca-912c-7043390e8cd3

OnProcessWorkItemAsync - Processing orchestrator action of type OrchestrationComplete
EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : 16af7b96-9409-4618-b33a-e7cc3a121a1a] [executionId : 410f7f47fdd9444ab3fb3ce6d00e9649] [sessionId : ] [message : Instance Id '[InstanceId: 16af7b96-9409-4618-b33a-e7cc3a121a1a, ExecutionId: 410f7f47fdd9444ab3fb3ce6d00e9649]' completed in state Completed with result: "Greeting sent to sky"] [info : ] [eventType : TaskOrchestrationDispatcher-InstanceCompleted] , EventName : InfoInfo, Timestamp : 2024-04-28T01:51:10.0499421Z, ProcessId : 6536, ThreadId : 12032, ActivityId : 4d43f051-56be-45ca-912c-7043390e8cd3

EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : 16af7b96-9409-4618-b33a-e7cc3a121a1a] [executionId : 410f7f47fdd9444ab3fb3ce6d00e9649] [sessionId : ] [message : {{
  "$type": "DurableTask.Core.OrchestrationRuntimeStateDump, DurableTask.Core",
  "Events": [
    {{
      "$type": "DurableTask.Core.History.OrchestratorStartedEvent, DurableTask.Core",
      "EventType": 12,
      "EventId": -1,
      "IsPlayed": false,
      "Timestamp": "2024-04-28T01:50:59.3312077Z"
    }},
    {{
      "$type": "DurableTask.Core.History.ExecutionStartedEvent, DurableTask.Core",
      "OrchestrationInstance": null,
      "EventType": 0,
      "ParentInstance": null,
      "Name": null,
      "Version": null,
      "Input": "[..snipped..]",
      "Tags": null,
      "Correlation": null,
      "ParentTraceContext": null,
      "ScheduledStartTime": null,
      "Generation": null,
      "EventId": -1,
      "IsPlayed": true,
      "Timestamp": "2024-04-28T01:50:54.3937812Z"
    }},
    {{
      "$type": "DurableTask.Core.History.TaskScheduledEvent, DurableTask.Core",
      "EventType": 4,
      "Name": "DurableTask.Samples.Greetings.GetUserTask",
      "Version": "",
      "Input": "[..snipped..]",
      "ParentTraceContext": null,
      "EventId": 0,
      "IsPlayed": true,
      "Timestamp": "2024-04-28T01:50:59.3468324Z"
    }},
    {{
      "$type": "DurableTask.Core.History.OrchestratorCompletedEvent, DurableTask.Core",
      "EventType": 13,
      "EventId": -1,
      "IsPlayed": true,
      "Timestamp": "2024-04-28T01:50:59.3468324Z"
    }},
    {{
      "$type": "DurableTask.Core.History.OrchestratorStartedEvent, DurableTask.Core",
      "EventType": 12,
      "EventId": -1,
      "IsPlayed": false,
      "Timestamp": "2024-04-28T01:51:04.5343127Z"
    }},
    {{
      "$type": "DurableTask.Core.History.TaskCompletedEvent, DurableTask.Core",
      "EventType": 5,
      "TaskScheduledId": 0,
      "Result": "[..snipped..]",
      "EventId": -1,
      "IsPlayed": true,
      "Timestamp": "2024-04-28T01:51:04.1905617Z"
    }},
    {{
      "$type": "DurableTask.Core.History.TaskScheduledEvent, DurableTask.Core",
      "EventType": 4,
      "Name": "DurableTask.Samples.Greetings.SendGreetingTask",
      "Version": "",
      "Input": "[..snipped..]",
      "ParentTraceContext": null,
      "EventId": 1,
      "IsPlayed": true,
      "Timestamp": "2024-04-28T01:51:04.5343127Z"
    }},
    {{
      "$type": "DurableTask.Core.History.OrchestratorCompletedEvent, DurableTask.Core",
      "EventType": 13,
      "EventId": -1,
      "IsPlayed": true,
      "Timestamp": "2024-04-28T01:51:04.5343127Z"
    }},
    {{
      "$type": "DurableTask.Core.History.OrchestratorStartedEvent, DurableTask.Core",
      "EventType": 12,
      "EventId": -1,
      "IsPlayed": false,
      "Timestamp": "2024-04-28T01:51:10.0499421Z"
    }},
    {{
      "$type": "DurableTask.Core.History.TaskCompletedEvent, DurableTask.Core",
      "EventType": 5,
      "TaskScheduledId": 1,
      "Result": "[..snipped..]",
      "EventId": -1,
      "IsPlayed": true,
      "Timestamp": "2024-04-28T01:51:09.7222239Z"
    }},
    {{
      "$type": "DurableTask.Core.History.ExecutionCompletedEvent, DurableTask.Core",
      "EventType": 1,
      "OrchestrationStatus": 1,
      "Result": "[..snipped..]",
      "FailureDetails": null,
      "EventId": 2,
      "IsPlayed": false,
      "Timestamp": "2024-04-28T01:51:10.0499421Z"
    }}
  ],
  "NewEvents": [
    {{
      "$type": "DurableTask.Core.History.OrchestratorStartedEvent, DurableTask.Core",
      "EventType": 12,
      "EventId": -1,
      "IsPlayed": false,
      "Timestamp": "2024-04-28T01:51:10.0499421Z"
    }},
    {{
      "$type": "DurableTask.Core.History.TaskCompletedEvent, DurableTask.Core",
      "EventType": 5,
      "TaskScheduledId": 1,
      "Result": "[..snipped..]",
      "EventId": -1,
      "IsPlayed": true,
      "Timestamp": "2024-04-28T01:51:09.7222239Z"
    }},
    {{
      "$type": "DurableTask.Core.History.ExecutionCompletedEvent, DurableTask.Core",
      "EventType": 1,
      "OrchestrationStatus": 1,
      "Result": "[..snipped..]",
      "FailureDetails": null,
      "EventId": 2,
      "IsPlayed": false,
      "Timestamp": "2024-04-28T01:51:10.0499421Z"
    }}
  ],
  "EventCount": 0,
  "NewEventsCount": 0
}}] [info : ] [eventType : TaskOrchestrationDispatcher-InstanceCompletionEvents] , EventName : InfoInfo, Timestamp : 2024-04-28T01:51:10.0499421Z, ProcessId : 6536, ThreadId : 12032, ActivityId : 4d43f051-56be-45ca-912c-7043390e8cd3

EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : 16af7b96-9409-4618-b33a-e7cc3a121a1a] [message : Deleting session state] [info : ] [eventType : TaskOrchestrationDispatcher-DeletingSessionState] , EventName : InfoInfo, Timestamp : 2024-04-28T01:51:10.0499421Z, ProcessId : 6536, ThreadId : 12032, ActivityId : 4d43f051-56be-45ca-912c-7043390e8cd3

EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskOrchestrationDispatcher-e0bc782c9aa84a0a8a256cf7a5d4e06e-0: Finished processing workItem 16af7b96-9409-4618-b33a-e7cc3a121a1a] [info : ] [eventType : WorkItemDispatcherProcess-End] , EventName : InfoInfo, Timestamp : 2024-04-28T01:51:10.3624424Z, ProcessId : 6536, ThreadId : 13976, ActivityId : 4d43f051-56be-45ca-912c-7043390e8cd3

Task done: Completed
Press any key to quit.


```


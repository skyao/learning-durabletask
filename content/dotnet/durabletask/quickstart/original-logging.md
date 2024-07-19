---
title: "quickstart原始日志"
linkTitle: "原始日志"
weight: 20
date: 2024-01-19
description: >
  记录durabletask Quickstart的原始日志
---



命令开始执行：

```bash
$ dotnet run -c debug -s Greetings

Start Orchestration: Greetings
```

日志为：

```bash
 dotnet run -c debug -s Greetings
sleep 30 seconds
waiting for debugger to attach
done waiting for debugger to attach
Start Orchestration: Greetings
Workflow Instance Started: [InstanceId: 16af7b96-9409-4618-b33a-e7cc3a121a1a, ExecutionId: 410f7f47fdd9444ab3fb3ce6d00e9649]
EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : WorkItemDispatcher('TaskOrchestrationDispatcher') starting. Id e0bc782c9aa84a0a8a256cf7a5d4e06e.] [info : ] [eventType : WorkItemDispatcherStart] , EventName : InfoInfo, Timestamp : 2024-04-28T01:50:56.3468829Z, ProcessId : 6536, ThreadId : 14068

EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : WorkItemDispatcher('TaskActivityDispatcher') starting. Id 38b3e9add82043708a8643b81cf2f643.] [info : ] [eventType : WorkItemDispatcherStart] , EventName : InfoInfo, Timestamp : 2024-04-28T01:50:56.3625156Z, ProcessId : 6536, ThreadId : 14068

Waiting up to 60 seconds for completion.
EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskOrchestrationDispatcher-e0bc782c9aa84a0a8a256cf7a5d4e06e-0: Starting fetch with timeout of 00:00:30 (0/100 max)] [info : ] [eventType : WorkItemDispatcherDispatch-StartFetch] , EventName : TraceInfo, Timestamp : 2024-04-28T01:50:56.3625156Z, ProcessId : 6536, ThreadId : 12032

EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskActivityDispatcher-38b3e9add82043708a8643b81cf2f643-0: Starting fetch with timeout of 00:00:30 (0/10 max)] [info : ] [eventType : WorkItemDispatcherDispatch-StartFetch] , EventName : TraceInfo, Timestamp : 2024-04-28T01:50:56.3625156Z, ProcessId : 6536, ThreadId : 9632

********* versioning  ************* LockNextTaskOrchestrationWorkItemAsync()
EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskOrchestrationDispatcher-e0bc782c9aa84a0a8a256cf7a5d4e06e-0: After fetch (2961 ms) (0/100 max)] [info : ] [eventType : WorkItemDispatcherDispatch-EndFetch] , EventName : TraceInfo, Timestamp : 2024-04-28T01:50:59.3155825Z, ProcessId : 6536, ThreadId : 12032, ActivityId : d85171a0-5ef1-4e78-a32b-0d31cb396de7

EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskOrchestrationDispatcher-e0bc782c9aa84a0a8a256cf7a5d4e06e-0: Starting fetch with timeout of 00:00:30 (1/100 max)] [info : ] [eventType : WorkItemDispatcherDispatch-StartFetch] , EventName : TraceInfo, Timestamp : 2024-04-28T01:50:59.3155825Z, ProcessId : 6536, ThreadId : 12032, ActivityId : d85171a0-5ef1-4e78-a32b-0d31cb396de7

EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskOrchestrationDispatcher-e0bc782c9aa84a0a8a256cf7a5d4e06e-0: Starting to process workItem 16af7b96-9409-4618-b33a-e7cc3a121a1a] [info : ] [eventType : WorkItemDispatcherProcess-Begin] , EventName : InfoInfo, Timestamp : 2024-04-28T01:50:59.3155825Z, ProcessId : 6536, ThreadId : 12032, ActivityId : d85171a0-5ef1-4e78-a32b-0d31cb396de7

EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : 16af7b96-9409-4618-b33a-e7cc3a121a1a] [executionId : 410f7f47fdd9444ab3fb3ce6d00e9649] [sessionId : ] [message : Processing new event with Id -1 and type ExecutionStarted] [info : ] [eventType : TaskOrchestrationDispatcher-ProcessEvent] , EventName : InfoInfo, Timestamp : 2024-04-28T01:50:59.3312077Z, ProcessId : 6536, ThreadId : 12032, ActivityId : d85171a0-5ef1-4e78-a32b-0d31cb396de7

EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : 16af7b96-9409-4618-b33a-e7cc3a121a1a] [executionId : 410f7f47fdd9444ab3fb3ce6d00e9649] [sessionId : ] [message : Executing user orchestration: {
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
}] [info : ] [eventType : TaskOrchestrationDispatcher-ExecuteUserOrchestration-Begin] , EventName : TraceInfo, Timestamp : 2024-04-28T01:50:59.3312077Z, ProcessId : 6536, ThreadId : 12032, ActivityId : d85171a0-5ef1-4e78-a32b-0d31cb396de7

ProcessEvent: EventType=ExecutionStarted
GreetingsOrchestration.RunTask called
EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : 16af7b96-9409-4618-b33a-e7cc3a121a1a] [executionId : 410f7f47fdd9444ab3fb3ce6d00e9649] [sessionId : ] [message : Executed user orchestration. Received 1 orchestrator actions: 0:ScheduleOrchestrator] [info : ] [eventType : TaskOrchestrationDispatcher-ExecuteUserOrchestration-End] , EventName : InfoInfo, Timestamp : 2024-04-28T01:50:59.3468324Z, ProcessId : 6536, ThreadId : 12032, ActivityId : d85171a0-5ef1-4e78-a32b-0d31cb396de7

EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : 16af7b96-9409-4618-b33a-e7cc3a121a1a] [executionId : 410f7f47fdd9444ab3fb3ce6d00e9649] [sessionId : ] [message : Processing orchestrator action of type ScheduleOrchestrator] [info : ] [eventType : TaskOrchestrationDispatcher-ProcessOrchestratorAction] , EventName : InfoInfo, Timestamp : 2024-04-28T01:50:59.3468324Z, ProcessId : 6536, ThreadId : 12032, ActivityId : d85171a0-5ef1-4e78-a32b-0d31cb396de7

OnProcessWorkItemAsync - Processing orchestrator action of type ScheduleOrchestrator
EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskActivityDispatcher-38b3e9add82043708a8643b81cf2f643-0: After fetch (3161 ms) (0/10 max)] [info : ] [eventType : WorkItemDispatcherDispatch-EndFetch] , EventName : TraceInfo, Timestamp : 2024-04-28T01:50:59.5187069Z, ProcessId : 6536, ThreadId : 13976, ActivityId : 1e0c99d8-4905-4149-a790-332669c907af

EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskActivityDispatcher-38b3e9add82043708a8643b81cf2f643-0: Starting fetch with timeout of 00:00:30 (1/10 max)] [info : ] [eventType : WorkItemDispatcherDispatch-StartFetch] , EventName : TraceInfo, Timestamp : 2024-04-28T01:50:59.5187069Z, ProcessId : 6536, ThreadId : 13976, ActivityId : 1e0c99d8-4905-4149-a790-332669c907af

EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskActivityDispatcher-38b3e9add82043708a8643b81cf2f643-0: Starting to process workItem 585e3de9-e953-4f39-af4f-c4a9b7bcd657] [info : ] [eventType : WorkItemDispatcherProcess-Begin] , EventName : InfoInfo, Timestamp : 2024-04-28T01:50:59.5187069Z, ProcessId : 6536, ThreadId : 12032, ActivityId : d85171a0-5ef1-4e78-a32b-0d31cb396de7

Waiting for user to enter name...
EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskOrchestrationDispatcher-e0bc782c9aa84a0a8a256cf7a5d4e06e-0: Finished processing workItem 16af7b96-9409-4618-b33a-e7cc3a121a1a] [info : ] [eventType : WorkItemDispatcherProcess-End] , EventName : InfoInfo, Timestamp : 2024-04-28T01:50:59.8155822Z, ProcessId : 6536, ThreadId : 13976, ActivityId : 1e0c99d8-4905-4149-a790-332669c907af

User Name Entered: sky
EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskActivityDispatcher-38b3e9add82043708a8643b81cf2f643-0: Finished processing workItem 585e3de9-e953-4f39-af4f-c4a9b7bcd657] [info : ] [eventType : WorkItemDispatcherProcess-End] , EventName : InfoInfo, Timestamp : 2024-04-28T01:51:04.3624392Z, ProcessId : 6536, ThreadId : 11248

********* versioning  ************* LockNextTaskOrchestrationWorkItemAsync()
EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskOrchestrationDispatcher-e0bc782c9aa84a0a8a256cf7a5d4e06e-0: After fetch (5214 ms) (0/100 max)] [info : ] [eventType : WorkItemDispatcherDispatch-EndFetch] , EventName : TraceInfo, Timestamp : 2024-04-28T01:51:04.5343127Z, ProcessId : 6536, ThreadId : 14068, ActivityId : f698e9a6-907c-4092-bd48-e19e7ba6a2a9

EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskOrchestrationDispatcher-e0bc782c9aa84a0a8a256cf7a5d4e06e-0: Starting fetch with timeout of 00:00:30 (1/100 max)] [info : ] [eventType : WorkItemDispatcherDispatch-StartFetch] , EventName : TraceInfo, Timestamp : 2024-04-28T01:51:04.5343127Z, ProcessId : 6536, ThreadId : 14068, ActivityId : f698e9a6-907c-4092-bd48-e19e7ba6a2a9

EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskOrchestrationDispatcher-e0bc782c9aa84a0a8a256cf7a5d4e06e-0: Starting to process workItem 16af7b96-9409-4618-b33a-e7cc3a121a1a] [info : ] [eventType : WorkItemDispatcherProcess-Begin] , EventName : InfoInfo, Timestamp : 2024-04-28T01:51:04.5343127Z, ProcessId : 6536, ThreadId : 9632, ActivityId : fec9f5fa-cfa6-4115-95f1-7f40e3e9d6aa

EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : 16af7b96-9409-4618-b33a-e7cc3a121a1a] [executionId : 410f7f47fdd9444ab3fb3ce6d00e9649] [sessionId : ] [message : Processing new event with Id -1 and type TaskCompleted] [info : ] [eventType : TaskOrchestrationDispatcher-ProcessEvent] , EventName : InfoInfo, Timestamp : 2024-04-28T01:51:04.5343127Z, ProcessId : 6536, ThreadId : 9632, ActivityId : fec9f5fa-cfa6-4115-95f1-7f40e3e9d6aa

EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : 16af7b96-9409-4618-b33a-e7cc3a121a1a] [executionId : 410f7f47fdd9444ab3fb3ce6d00e9649] [sessionId : ] [message : Executing user orchestration: {
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
}] [info : ] [eventType : TaskOrchestrationDispatcher-ExecuteUserOrchestration-Begin] , EventName : TraceInfo, Timestamp : 2024-04-28T01:51:04.5343127Z, ProcessId : 6536, ThreadId : 9632, ActivityId : fec9f5fa-cfa6-4115-95f1-7f40e3e9d6aa

ProcessEvent: EventType=ExecutionStarted
GreetingsOrchestration.RunTask called
ProcessEvent: EventType=TaskScheduled
ProcessEvent: EventType=OrchestratorCompleted
ProcessEvent: EventType=TaskCompleted
EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : 16af7b96-9409-4618-b33a-e7cc3a121a1a] [executionId : 410f7f47fdd9444ab3fb3ce6d00e9649] [sessionId : ] [message : Executed user orchestration. Received 1 orchestrator actions: 1:ScheduleOrchestrator] [info : ] [eventType : TaskOrchestrationDispatcher-ExecuteUserOrchestration-End] , EventName : InfoInfo, Timestamp : 2024-04-28T01:51:04.5343127Z, ProcessId : 6536, ThreadId : 9632, ActivityId : fec9f5fa-cfa6-4115-95f1-7f40e3e9d6aa

EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : 16af7b96-9409-4618-b33a-e7cc3a121a1a] [executionId : 410f7f47fdd9444ab3fb3ce6d00e9649] [sessionId : ] [message : Processing orchestrator action of type ScheduleOrchestrator] [info : ] [eventType : TaskOrchestrationDispatcher-ProcessOrchestratorAction] , EventName : InfoInfo, Timestamp : 2024-04-28T01:51:04.5343127Z, ProcessId : 6536, ThreadId : 9632, ActivityId : fec9f5fa-cfa6-4115-95f1-7f40e3e9d6aa

OnProcessWorkItemAsync - Processing orchestrator action of type ScheduleOrchestrator
EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskActivityDispatcher-38b3e9add82043708a8643b81cf2f643-0: After fetch (5183 ms) (0/10 max)] [info : ] [eventType : WorkItemDispatcherDispatch-EndFetch] , EventName : TraceInfo, Timestamp : 2024-04-28T01:51:04.7061869Z, ProcessId : 6536, ThreadId : 11248, ActivityId : 413409db-6e47-413a-b78d-319a802d563c

EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskActivityDispatcher-38b3e9add82043708a8643b81cf2f643-0: Starting fetch with timeout of 00:00:30 (1/10 max)] [info : ] [eventType : WorkItemDispatcherDispatch-StartFetch] , EventName : TraceInfo, Timestamp : 2024-04-28T01:51:04.7061869Z, ProcessId : 6536, ThreadId : 11248, ActivityId : 413409db-6e47-413a-b78d-319a802d563c

EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskActivityDispatcher-38b3e9add82043708a8643b81cf2f643-0: Starting to process workItem d9bb55c6-f35e-495b-994e-b6bd106372b1] [info : ] [eventType : WorkItemDispatcherProcess-Begin] , EventName : InfoInfo, Timestamp : 2024-04-28T01:51:04.7061869Z, ProcessId : 6536, ThreadId : 9632, ActivityId : f698e9a6-907c-4092-bd48-e19e7ba6a2a9

Sending greetings to user: sky...
EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskOrchestrationDispatcher-e0bc782c9aa84a0a8a256cf7a5d4e06e-0: Finished processing workItem 16af7b96-9409-4618-b33a-e7cc3a121a1a] [info : ] [eventType : WorkItemDispatcherProcess-End] , EventName : InfoInfo, Timestamp : 2024-04-28T01:51:04.9093117Z, ProcessId : 6536, ThreadId : 13976, ActivityId : f698e9a6-907c-4092-bd48-e19e7ba6a2a9

Greeting sent to sky
EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskActivityDispatcher-38b3e9add82043708a8643b81cf2f643-0: Finished processing workItem d9bb55c6-f35e-495b-994e-b6bd106372b1] [info : ] [eventType : WorkItemDispatcherProcess-End] , EventName : InfoInfo, Timestamp : 2024-04-28T01:51:09.8780623Z, ProcessId : 6536, ThreadId : 10076, ActivityId : 1946c0da-30f0-4767-9aff-a5ff1e2bb884

********* versioning  ************* LockNextTaskOrchestrationWorkItemAsync()
EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskOrchestrationDispatcher-e0bc782c9aa84a0a8a256cf7a5d4e06e-0: After fetch (5515 ms) (0/100 max)] [info : ] [eventType : WorkItemDispatcherDispatch-EndFetch] , EventName : TraceInfo, Timestamp : 2024-04-28T01:51:10.0499421Z, ProcessId : 6536, ThreadId : 12032, ActivityId : 4d43f051-56be-45ca-912c-7043390e8cd3

EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskOrchestrationDispatcher-e0bc782c9aa84a0a8a256cf7a5d4e06e-0: Starting fetch with timeout of 00:00:30 (1/100 max)] [info : ] [eventType : WorkItemDispatcherDispatch-StartFetch] , EventName : TraceInfo, Timestamp : 2024-04-28T01:51:10.0499421Z, ProcessId : 6536, ThreadId : 12032, ActivityId : 4d43f051-56be-45ca-912c-7043390e8cd3

EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : ] [executionId : ] [sessionId : ] [message : TaskOrchestrationDispatcher-e0bc782c9aa84a0a8a256cf7a5d4e06e-0: Starting to process workItem 16af7b96-9409-4618-b33a-e7cc3a121a1a] [info : ] [eventType : WorkItemDispatcherProcess-Begin] , EventName : InfoInfo, Timestamp : 2024-04-28T01:51:10.0499421Z, ProcessId : 6536, ThreadId : 12032, ActivityId : 4d43f051-56be-45ca-912c-7043390e8cd3

EventId : 3, Level : Informational, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : 16af7b96-9409-4618-b33a-e7cc3a121a1a] [executionId : 410f7f47fdd9444ab3fb3ce6d00e9649] [sessionId : ] [message : Processing new event with Id -1 and type TaskCompleted] [info : ] [eventType : TaskOrchestrationDispatcher-ProcessEvent] , EventName : InfoInfo, Timestamp : 2024-04-28T01:51:10.0499421Z, ProcessId : 6536, ThreadId : 12032, ActivityId : 4d43f051-56be-45ca-912c-7043390e8cd3

EventId : 1, Level : Verbose, Message : , Payload : [source : DurableTask.Core-durabletask.samples] [instanceId : 16af7b96-9409-4618-b33a-e7cc3a121a1a] [executionId : 410f7f47fdd9444ab3fb3ce6d00e9649] [sessionId : ] [message : Executing user orchestration: {
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
}] [info : ] [eventType : TaskOrchestrationDispatcher-ExecuteUserOrchestration-Begin] , EventName : TraceInfo, Timestamp : 2024-04-28T01:51:10.0499421Z, ProcessId : 6536, ThreadId : 12032, ActivityId : 4d43f051-56be-45ca-912c-7043390e8cd3

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


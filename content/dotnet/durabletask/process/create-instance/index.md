---
title: "start instance 流程"
linkTitle: "start instance"
weight: 10
date: 2024-01-19
description: >
  start instance 流程
---



## 入口



```c#
instance = taskHubClient.CreateOrchestrationInstanceAsync(typeof(GreetingsOrchestration), instanceId, null).Result;
```



进入 `src\DurableTask.Core\TaskHubClient.cs`

```c#
async Task<OrchestrationInstance> InternalCreateOrchestrationInstanceWithRaisedEventAsync() {
  				......
              try
            {
                // Raised events and create orchestration calls use different methods so get handled separately
                await this.ServiceClient.CreateTaskOrchestrationAsync(startMessage, dedupeStatuses);
            }
}
```

进入 `src\DurableTask.AzureStorage\AzureStorageOrchestrationService.cs` 

```c#
        public async Task CreateTaskOrchestrationAsync(TaskMessage creationMessage, OrchestrationStatus[] dedupeStatuses)
        {
            ExecutionStartedEvent executionStartedEvent = creationMessage.Event as ExecutionStartedEvent;
          
          ......
            ControlQueue controlQueue = await this.GetControlQueueAsync(creationMessage.OrchestrationInstance.InstanceId);
            //controlQueue 的 name 是类似 sampleshub-control-01
            MessageData startMessage = await this.SendTaskOrchestrationMessageInternalAsync(
                EmptySourceInstance,
                controlQueue,
                creationMessage);
          
        }
```





```c#
        Task<MessageData> SendTaskOrchestrationMessageInternalAsync(
            OrchestrationInstance sourceInstance,
            ControlQueue controlQueue,
            TaskMessage message)
        {
            return controlQueue.AddMessageAsync(message, sourceInstance);
        }
```



`src\DurableTask.AzureStorage\Messaging\TaskHubQueue.cs` 

```c#
        public Task<MessageData> AddMessageAsync(TaskMessage message, OrchestrationInstance sourceInstance)
        {
            return this.AddMessageAsync(message, sourceInstance, session: null);
        }

        async Task<MessageData> AddMessageAsync(TaskMessage taskMessage, OrchestrationInstance sourceInstance, SessionBase? session)
        {
          		......
              await this.storageQueue.AddMessageAsync(
                    queueMessage,
                    GetVisibilityDelay(taskMessage),
                    session?.TraceActivityId);
          
        }
```



## 总结

最后是给 controlQueue 里面发了一个 携带 ExecutionStartedEvent 事件的 Message。 

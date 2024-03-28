---
title: "特性-等待外部事件"
linkTitle: "特性-等待外部事件"
weight: 130
date: 2024-01-19
description: >
  在 Durable Task Framework 中实现等待外部事件的特性
---

> https://github.com/Azure/durabletask/wiki/Feature---Waiting-on-External-Events-%28Human-Interaction%29

通常情况下，协调需要等待外部事件，如人类输入某些输入或其他外部触发。该框架为协调提供了一种异步等待外部事件的机制。

```c#
public class GetQuoteOrchestration : TaskOrchestration<string, string>
{
    TaskCompletionSource<object> getPermission = new TaskCompletionSource<object>(); 

    public override async Task<string> RunTask(OrchestrationContext context, string input)
    {
        await getPermission.Task;
        await context.ScheduleTask<object>(typeof (GetQuote), null);
        return null;
    }

    public override void OnEvent(OrchestrationContext context, string name, string input)
    {
        getPermission.SetResult(null);
    }
}
```

要从外部触发事件，用户可以调用 TaskHubClient.RaiseEvent 方法。

```c#
TaskHubClient client = new TaskHubClient("test", serviceBusConnString);
OrchestrationInstance instance = client.CreateOrchestrationInstance(typeof (GetQuoteOrchestration),  "quote")
client.RaiseEvent(instance, "dummyEvent", "dummyData");
```
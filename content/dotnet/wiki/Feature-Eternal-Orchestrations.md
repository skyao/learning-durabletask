---
title: "特性-永恒编排"
linkTitle: "特性-永恒编排"
weight: 130
date: 2024-01-19
description: >
  在 Durable Task Framework 中实现永恒编排（又名无限循环）的特性
---

> https://github.com/Azure/durabletask/wiki/Feature---Eternal-Orchestrations-%28aka-infinite-loops%29

如《编写任务编排》一文所述，框架会重播执行历史，为用户的任务编排实例重新创建程序状态。历史记录的大小是有限制的，因此不可能出现无限循环的任务编排类。

利用生成功能，用户可以 "检查点" 协调实例并创建新的实例。

```c#
public class CronOrchestration : TaskOrchestration<string, int>
{
    public override async Task<string> RunTask(OrchestrationContext context, int intervalHours)
    {
        // bounded loop
        for (int i = 0; i < 10; i++)
        {
            await context.CreateTimer<object>(
                context.CurrentUtcDateTime.Add(TimeSpan.FromHours(intervalHours)), null);
            // TODO : do something interesting 
        }

        // create a new instance of self with the same input (or different if needed)
        context.ContinueAsNew(intervalHours);
        return null;
    }
}
```

在这个片段中，用户告诉框架为自己创建一个全新的实例（即新一代或执行），并将收到的输入作为输入转发给新实例。这个协调过程可以无限期运行，而不会受到历史记录大小的限制。





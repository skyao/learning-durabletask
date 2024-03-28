---
title: "特性-自动重试"
linkTitle: "特性-自动重试"
weight: 100
date: 2024-01-19
description: >
  在 Durable Task Framework 中实现自动重试的特性
---

> https://github.com/Azure/durabletask/wiki/Feature---Automatic-Retries

任何使用云服务的应用程序都应在一定程度上对故障具有弹性，因此客户端重试成为实施的重要部分。

该框架提供了替代调度方法，可根据提供的策略在任务活动失败时执行重试。如果您需要自动重试，例如从网络服务读取数据或向数据库执行空闲写入的任务活动，这将非常有用。

```c#
public class GetQuoteOrchestration : TaskOrchestration<string, string>
{
    public override async Task<string> RunTask(OrchestrationContext context, string input)
    {
        // retry every 10 seconds upto 5 times before giving up and bubbling up the exception
        RetryOptions retryOptions = new RetryOptions(TimeSpan.FromSeconds(10), 5);
        await context.ScheduleWithRetry<object>(typeof (GetQuote), retryOptions, null);
        return null;
    }
}
```
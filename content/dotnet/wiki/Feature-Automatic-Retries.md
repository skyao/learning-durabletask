---
title: "特性-持久计时器"
linkTitle: "特性-持久计时器"
weight: 110
date: 2024-01-19
description: >
  在 Durable Task Framework 中实现持久计时器的特性
---

> https://github.com/Azure/durabletask/wiki/Feature---Durable-Timers

用户可以在协调代码中等待异步定时器事件。

```c#
public class EncodeVideoOrchestration : TaskOrchestration<string, string>
{
    public override async Task<string> RunTask(OrchestrationContext context, string input)
    {
        string encodedUrl = await context.ScheduleTask<string>(typeof (EncodeActivity), input);
        await context.CreateTimer(context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1)), "timer1");
        await context.ScheduleTask<object>(typeof (EmailActivity), input);
                
        return encodedUrl;
    }
}
```

等待 CreateTimer 任务的行将导致协调在编码视频和电子邮件活动之间休眠一天。

定时器可用于定期工作和超时。

```c#
public class BillingOrchestration : TaskOrchestration<string, string>
{
    public override async Task<string> RunTask(OrchestrationContext context, string input)
    {
        for (int i = 0; i < 10; i++)
        {
            await context.CreateTimer(context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1)), "timer1");
            await context.ScheduleTask<object>(typeof (BillingActivity));
        }
        return null;
    }
}
```

在上面的片段中，计费协调将每天发出信号，并在唤醒时调用一些计费活动。

```c#
public class GetQuoteOrchestration : TaskOrchestration<string, string>
{
    public override async Task<string> RunTask(OrchestrationContext context, string input)
    {
        CancellationTokenSource cancellationTokenForTimer = new CancellationTokenSource();
        Task timer = context.CreateTimer(
            context.CurrentUtcDateTime.Add(TimeSpan.FromSeconds(5)), "timer1", cancellationTokenForTimer.Token);
        Task getQuote = context.ScheduleTask<object>(typeof(GetQuote));
        Task winner = await Task.WhenAny(timer, getQuote);
        if (timer.IsCompleted)
        {
            // request timed out, do some compensating action
        }
        else
        {
            // without this, timer will still block
            // orchestration completion
            cancellationTokenForTimer.Cancel();

            // use getQuote task result
        }
        return null;
    }
}
```

在此代码段中，我们安排了 GetQuote 活动，并创建了一个 5 秒后触发的计时器。如果定时器在活动返回前触发，我们就运行一些补偿，否则就使用返回的报价。

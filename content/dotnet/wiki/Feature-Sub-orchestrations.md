---
title: "特性-子编排"
linkTitle: "特性-子编排"
weight: 140
date: 2024-01-19
description: >
  在 Durable Task Framework 中实现子编排（又名内嵌编排）的特性
---

> https://github.com/Azure/durabletask/wiki/Feature---Sub-orchestrations-%28aka-nested-orchestrations%29

协调还可以使用子协调功能启动和等待其他协调。如果您有一个协调库，并希望围绕这些协调库构建一个更大的协调，这将非常有用。

```c#
public class PeriodicBillingJob : TaskOrchestration<string, int>
{
    // hardcoded list of apps to run billing orchestrations on
    static readonly string[] ApplicationList = new string[] { "app1", "app2" };

    public override async Task<string> RunTask(OrchestrationContext context, int intervalHours)
    {
        // bounded loop
        for (int i = 0; i < 10; i++)
        {
            await context.CreateTimer<object>(
                context.CurrentUtcDateTime.Add(TimeSpan.FromHours(intervalHours)), null);

            List<Task> billingTasks = new List<Task>();

            foreach (string appName in PeriodicBillingJob.ApplicationList)
            {
                billingTasks.Add(
                    context.CreateSubOrchestrationInstance<bool>(typeof (BillingOrchestration), appName));
            }
            await Task.WhenAll(billingTasks);
        }

        // create a new instance of self with the same input (or different if needed)
        context.ContinueAsNew(intervalHours);
        return null;
    }
}

// a reusable orchestration which can either be triggered directly by the admin or via 
// some master recurring periodic billing orchestration
public class BillingOrchestration : TaskOrchestration<bool, string>
{
    public override async Task<bool> RunTask(OrchestrationContext context, string applicationName)
    {
        // TODO : process billing information for 'applicationName'
        return true;
    }
}
```
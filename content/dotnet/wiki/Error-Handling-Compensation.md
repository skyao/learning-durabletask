---
title: "错误处理和补偿"
linkTitle: "错误处理和补偿"
weight: 70
date: 2024-01-19
description: >
  在 Durable Task Framework 中进行错误处理和补偿
---

> https://github.com/Azure/durabletask/wiki/Error-Handling-&-Compensation

在 TaskActivity 代码中抛出的任何异常都会在 TaskOrchestration 代码中以 TaskFailedException 的形式回调和抛出。用户可以根据自己的需要编写适当的错误处理和补偿代码。

```c#
public class DebitCreditOrchestration : 
    TaskOrchestration<object, DebitCreditOperation>
{
    public override async Task<object> RunTask(OrchestrationContext context, 
        DebitCreditOperation operation)
    {
        bool failed = false;
        bool debited = false;
        try
        {
            await context.ScheduleTask<object>(typeof (DebitAccount),
                            new Tuple<string, float>(operation.SourceAccount, operation.Amount));
            debited = true;

            await context.ScheduleTask<object>(typeof(CreditAccount),
                            new Tuple<string, float>(operation.TargetAccount, operation.Amount));
        }
        catch (TaskFailedException exception)
        {
            if (debited)
            {
                // can build a try-catch around this as well, in which case the 
                // orchestration may either retry a few times or log the inconsistency for review
                await context.ScheduleTask<object>(typeof(CreditAccount),
                            new Tuple<string, float>(operation.SourceAccount, operation.Amount));
            }
        }

        return null;
    }
}
```

请注意，在 dotnet 6.0 之前，由于 CLR 的限制，不能在 catch 块中使用 await 关键字。在这种情况下，我们可以修改上面的代码，设置一个失败标志，并在返回结果前执行补偿：

```c#
# ... orchestration code here
        catch (TaskFailedException exception)
        {
            failed = true;
        }

        if (failed)
        {
            if (debited)
            {
                // can build a try-catch around this as well, in which case the 
                // orchestration may either retry a few times or log the inconsistency for review
                await context.ScheduleTask<object>(typeof(CreditAccount),
                            new Tuple<string, float>(operation.SourceAccount, operation.Amount));
            }
        }

        return null;
    }
}
```
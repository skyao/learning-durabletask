---
title: "TaskOrchestrationContext"
linkTitle: "TaskOrchestrationContext"
weight: 20
date: 2024-01-19
description: >
  TaskOrchestrationContext
---



`src\DurableTask.Core\TaskOrchestrationContext.cs`

## 类定义

TaskOrchestrationContext 继承自 OrchestrationContext：

```c#
    internal class TaskOrchestrationContext : OrchestrationContext
    {
    }
```



### Execute() 方法

```c#
public abstract Task<string> Execute(OrchestrationContext context, string input);
```

方法实现为：

```c#
public override async Task<string> Execute(OrchestrationContext context, string input)
        {
            var parameter = DataConverter.Deserialize<TInput>(input);
            TResult result;
            try
            {
                result = await RunTask(context, parameter);
            }
            catch (Exception e) when (!Utils.IsFatal(e) && !Utils.IsExecutionAborting(e))
            {
                string details = null;
                FailureDetails failureDetails = null;
                if (context.ErrorPropagationMode == ErrorPropagationMode.SerializeExceptions)
                {
                    details = Utils.SerializeCause(e, DataConverter);
                }
                else
                {
                    failureDetails = new FailureDetails(e);
                }

                throw new OrchestrationFailureException(e.Message, details)
                {
                    FailureDetails = failureDetails,
                };
            }

            return DataConverter.Serialize(result);
        }
```

RunTask() 方法是个抽象方法。

```c#
public abstract Task<TResult> RunTask(OrchestrationContext context, TInput input);
```

默认的 DataConverter 是 json：

```c#
    public abstract class TaskOrchestration<TResult, TInput, TEvent, TStatus> : TaskOrchestration
    {
        /// <summary>
        /// Creates a new TaskOrchestration with the default DataConverter
        /// </summary>
        protected TaskOrchestration()
        {
            DataConverter = JsonDataConverter.Default;
        }

        /// <summary>
        /// The DataConverter to use for input and output serialization/deserialization
        /// </summary>
        public DataConverter DataConverter { get; protected set; }
```





### RaiseEvent() 方法

```c#
public abstract void RaiseEvent(OrchestrationContext context, string name, string input);
```

方法实现为：

```c#
public override void RaiseEvent(OrchestrationContext context, string name, string input)
{
  var parameter = DataConverter.Deserialize<TEvent>(input);
  OnEvent(context, name, parameter);
}
```

OnEvent() 是一个空实现。

```c#
        public virtual void OnEvent(OrchestrationContext context, string name, TEvent input)
        {
            // do nothing
        }
```



### GetStatus() 方法

```c#
public abstract string GetStatus();
```



## 实现

在这个项目中除了 sample 和 test 外没有实现类。

在 `samples\Correlation.Samples\HelloOrchestrator.cs` 中有一个最简单的实现：

```c#
[KnownType(typeof(Hello))]
    internal class HelloOrchestrator : TaskOrchestration<string, string>
    {
        public override async Task<string> RunTask(OrchestrationContext context, string input)
        {
          //  await contextBase.ScheduleTask<string>(typeof(Hello), "world");
          //   if you pass an empty string it throws an error
            return await context.ScheduleTask<string>(typeof(Hello), "world");
        }
    }

    internal class Hello : TaskActivity<string, string>
    {
        protected override string Execute(TaskContext context, string input)
        {
            if (string.IsNullOrEmpty(input))
            {
                throw new ArgumentNullException(nameof(input));
            }

            Console.WriteLine($"Activity: Hello {input}");
            return $"Hello, {input}!";
        }
    }
```

> 备注：这个实现和 Dapr workflow java sdk 中的定义最贴近，也最适合用来增加一个 getVersion() 方法来获取当前 worker 的版本，但是为什么 quickstart 中用的是静态方法？
>
> TBD：请教一下 Chris。




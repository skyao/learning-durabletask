---
title: "TaskOrchestration"
linkTitle: "TaskOrchestration"
weight: 10
date: 2024-01-19
description: >
  核心编程模型之 TaskOrchestration
---



`src\DurableTask.Core\TaskOrchestration.cs`

TaskOrchestration.cs 中定义了三个方法：

- Execute()
- RaiseEvent() 
- GetStatus() 

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


---
title: "TaskActivity"
linkTitle: "TaskActivity"
weight: 10
date: 2024-01-19
description: >
  核心编程模型之 TaskActivity
---



`src\DurableTask.Core\TaskActivity.cs`

TaskActivity 中定义了三个方法：

- Run()
- RunAsync() 

### Run() 方法

```c#
public abstract string Run(TaskContext context, string input);
```

blocked for AsyncTaskActivity:

```c#
        /// <summary>
        /// Synchronous execute method, blocked for AsyncTaskActivity
        /// </summary>
        /// <returns>string.Empty</returns>
        public override string Run(TaskContext context, string input)
        {
            // will never run
            return string.Empty;
        }
```

### RunAsync() 方法

```c#
        public virtual Task<string> RunAsync(TaskContext context, string input)
        {
            return Task.FromResult(Run(context, input));
        }
```

会被覆盖为：

```c#
public override async Task<string> RunAsync(TaskContext context, string input)
        {
            TInput parameter = default(TInput);

            var jArray = Utils.ConvertToJArray(input);

            int parameterCount = jArray.Count;
            if (parameterCount > 1)
            {
                throw new TaskFailureException(
                    "TaskActivity implementation cannot be invoked due to more than expected input parameters.  Signature mismatch.");
            }
            
            if (parameterCount == 1)
            {
                JToken jToken = jArray[0];
                if (jToken is JValue jValue)
                {
                    parameter = jValue.ToObject<TInput>();
                }
                else
                {
                    string serializedValue = jToken.ToString();
                    parameter = DataConverter.Deserialize<TInput>(serializedValue);
                }
            }

            TResult result;
            try
            {
                result = await ExecuteAsync(context, parameter);
            }
            catch (Exception e) when (!Utils.IsFatal(e) && !Utils.IsExecutionAborting(e))
            {
                string details = null;
                FailureDetails failureDetails = null;
                if (context != null && context.ErrorPropagationMode == ErrorPropagationMode.SerializeExceptions)
                {
                    details = Utils.SerializeCause(e, DataConverter);
                }
                else
                {
                    failureDetails = new FailureDetails(e);
                }

                throw new TaskFailureException(e.Message, e, details)
                    .WithFailureDetails(failureDetails);
            }

            string serializedResult = DataConverter.Serialize(result);
            return serializedResult;
        }
    }
```

ExecuteAsync() 是一个abstract 方法：

```c#
        protected abstract Task<TResult> ExecuteAsync(TaskContext context, TInput input);
```



### GetStatus() 方法

```c#
public abstract string GetStatus();
```


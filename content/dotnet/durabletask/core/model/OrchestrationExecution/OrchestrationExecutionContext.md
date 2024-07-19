---
title: "OrchestrationExecutionContext"
linkTitle: "OrchestrationExecutionContext"
weight: 10
date: 2024-01-19
description: >
  OrchestrationExecutionContext
---



`src\DurableTask.Core\OrchestrationExecutionContext.cs`

*Context associated with the orchestration being executed.*

与正在执行的协调相关的上下文。



```c#
    [DataContract]
    public class OrchestrationExecutionContext
    {
    }
```



### OrchestrationTags()

这个类就定义了一个 OrchestrationTags 方法：

```c#

        /// <summary>
        /// Gets the orchestration tags
        /// </summary>
        [DataMember]
        public IDictionary<string, string> OrchestrationTags { get; internal set; }

```


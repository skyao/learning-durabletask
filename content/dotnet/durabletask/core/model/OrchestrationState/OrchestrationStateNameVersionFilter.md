---
title: "OrchestrationStateNameVersionFilter"
linkTitle: "OrchestrationStateNameVersionFilter"
weight: 40
date: 2024-01-19
description: >
  OrchestrationStateNameVersionFilter
---

`src\DurableTask.Core\OrchestrationStateInstanceFilter.cs`

*Filter for Orchestration Name and Version*

协调名称和版本过滤器

## 类定义

OrchestrationStateNameVersionFilter 实现了 OrchestrationStateQueryFilter

```c#
    public class OrchestrationStateNameVersionFilter : OrchestrationStateQueryFilter
    {
    }
```

### 构造函数

```c#
        // 使用默认设置创建 OrchestrationStateInstanceFilter 的新实例
				public OrchestrationStateInstanceFilter()
        {
            // default is exact match
            StartsWith = false;
        }
```

只定义有两个方法用来存取 InstanceId / ExecutionId 作为过滤器的匹配方式，另外 StartsWith 设置筛选器的匹配类型：

```c#

        public string Name { get; set; }

        public string Version { get; set; }
```




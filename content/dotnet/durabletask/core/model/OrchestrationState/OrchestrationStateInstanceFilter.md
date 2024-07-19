---
title: "OrchestrationStateInstanceFilter"
linkTitle: "OrchestrationStateInstanceFilter"
weight: 30
date: 2024-01-19
description: >
  OrchestrationStateInstanceFilter
---

`src\DurableTask.Core\OrchestrationStateInstanceFilter.cs`

Filter for Orchestration instance filter

协调实例过滤器

## 类定义

OrchestrationStateInstanceFilter 实现了 OrchestrationStateQueryFilter

```c#
    public class OrchestrationStateInstanceFilter : OrchestrationStateQueryFilter
    {
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
				// 获取或设置过滤器的 InstanceId
        public string InstanceId { get; set; }

				// 获取或设置过滤器的 ExecutionId
        public string ExecutionId { get; set; }

				// 获取或设置筛选器的匹配类型：以开始或精确匹配开始
        public bool StartsWith { get; set; }
```






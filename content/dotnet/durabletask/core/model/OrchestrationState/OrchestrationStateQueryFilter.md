---
title: "OrchestrationStateQueryFilter"
linkTitle: "OrchestrationStateQueryFilter"
weight: 20
date: 2024-01-19
description: >
  OrchestrationStateQueryFilter
---

`src\DurableTask.Core\OrchestrationStateQueryFilter.cs`

*Abstract class for an orchestration state query filter*

协调状态查询过滤器的抽象类

## 类定义

OrchestrationStateInstanceFilter 实现了 OrchestrationStateQueryFilter

```c#
    public abstract class OrchestrationStateQueryFilter
    {
    }
```

这是一个空的抽象类。

它的实现有：

- OrchestrationStateInstanceFilter
- OrchestrationStateNameVersionFilter: 这个可以用来做版本过滤

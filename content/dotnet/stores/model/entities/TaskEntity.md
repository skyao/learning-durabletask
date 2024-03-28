---
title: "TaskEntity"
linkTitle: "TaskEntity"
weight: 10
date: 2024-01-19
description: >
  核心编程模型之 TaskEntity
---

*Abstract base class for entities*

`src\DurableTask.Core\Entities\TaskEntity.cs`

TaskActivity 中定义了三个方法：

- ExecuteOperationBatchAsync()

### ExecuteOperationBatchAsync() 方法

```c#
public abstract Task<EntityBatchResult> ExecuteOperationBatchAsync(EntityBatchRequest operations);
```







### EnztityBatchRequest 类

*A request for execution of a batch of operations on an entity.*

- string InstanceId
- string EntityState
- `List<OperationRequest> Operations`





### OperationRequest 类

包含属性：

- string Operation
- Guid Id
- string Input








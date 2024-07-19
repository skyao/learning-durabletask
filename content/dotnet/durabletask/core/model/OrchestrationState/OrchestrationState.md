---
title: "OrchestrationState"
linkTitle: "OrchestrationState"
weight: 10
date: 2024-01-19
description: >
  核心编程模型的 OrchestrationState 定义
---

`src\DurableTask.Core\OrchestrationState.cs`

*Represents the state of an orchestration*

## 类定义

OrchestrationState 是一个 DataContract ：

```c#
    [DataContract]
    public class OrchestrationState : IExtensibleDataObject
    {
    }
```

定义有如下 DataMember：



| name                  | 类型                        | 描述                                          |
| --------------------- | --------------------------- | --------------------------------------------- |
| Name                  | string                      | 协调的名称                                    |
| Input                 | string                      | 协调的序列化输入                              |
| Output                | string                      | 协调的序列化输出                              |
|                       |                             |                                               |
| OrchestrationInstance | OrchestrationInstance       | 该状态代表的协调实例                          |
| OrchestrationStatus   | OrchestrationStatus         | 当前协调状态                                  |
| Status                | string                      | 字符串格式的当前协调状态                      |
| ParentInstance        | ParentInstance              | 父实例，如果这个协调有                        |
| Version               | string                      | 协调版本                                      |
| Tags                  | IDictionary<string, string> | 与该协调相关的标记和字符串值字典              |
| Generation            | int                         | 协调的代。重复使用的 instanceIds 将递增该值。 |
|                       |                             |                                               |
| CreatedTime           | DateTime                    | 协调的创建时间                                |
| ScheduledStartTime    | DateTime                    | 开始协调的时间                                |
| CompletedTime         | DateTime                    | 协调完成时间                                  |
| LastUpdatedTime       | DateTime                    | 协调的最后更新时间                            |
|                       |                             |                                               |
| Size                  | long                        | 原始（未压缩）序列化运行时状态的大小          |
| CompressedSize        | long                        | 压缩序列化运行时状态的大小                    |
|                       |                             |                                               |
| FailureDetails        | FailureDetails              | 获取或设置与协调相关的故障详细信息。          |
|                       |                             |                                               |
|                       |                             |                                               |
|                       |                             |                                               |
|                       |                             |                                               |



OrchestrationState有 Version 字段定义，另外 OrchestrationState 的 OrchestrationInstance 字段也带有 version。

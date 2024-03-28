---
title: "编写任务编排"
linkTitle: "编写任务编排"
weight: 40
date: 2024-01-19
description: >
  在 Durable Task Framework 中编写任务编排
---

> https://github.com/Azure/durabletask/wiki/Writing-Task-Orchestrations

任务协调基本上是调用任务活动，并定义控制如何从一个活动流向另一个活动。可以在协调中编写的代码是普通的 C#，但有一些限制。之所以存在这些限制，是因为框架是如何重播协调代码的。下面将对此进行简要说明。

每次协调需要处理新工作时（如任务活动完成或定时器启动），框架都会从头开始重播用户的任务协调代码。每当该用户代码尝试调度任务活动时，框架都会拦截该调用并查询协调的 "执行历史"。如果框架发现特定的任务活动已被执行并产生了一些结果，它就会立即重放该活动的结果，然后继续任务协调。这种情况会一直持续，直到用户代码执行到结束或计划了新的 Activity。如果是后一种情况，那么框架将实际安排并执行指定的 Activity。该活动完成后，其结果也将成为执行历史的一部分，其值将用于后续重放。

考虑到这一点，以下是我们可以在任务协调中编写的代码类型的限制：

- 代码必须具有确定性，因为它将被多次重放，每次都必须产生相同的结果。例如，不能直接调用获取当前日期/时间、随机数、Guids 或远程服务调用等。
- 传递给 `TaskOrchestration.RunTask()` 方法的 `OrchestrationContext` 对象上有一个辅助 API，它提供了一种获取当前日期/时间的确定性方法。应使用该对象代替 `System.DateTime`。
- 用户可以将非确定性操作封装在 TaskActivities 中，使其成为确定性操作。例如，GenerateGuidActivity、GenerateRandomNumberActivity 等。由于框架会重放任务活动的结果，因此非确定值将在首次执行时生成一次，然后在后续执行时重放相同的值。
- 未来，"OrchestrationContext "还将添加其他辅助 API。
- 代码应该是非阻塞的，即没有线程睡眠或 `Task.WaitXXX()` 方法。框架提供了设置异步计时器的辅助方法，应使用这些方法。
- 协调的执行历史记录包含所有已安排的任务活动及其结果。该历史记录还受到服务总线大小的限制（在使用服务总线提供程序时），因此，如果没有用户辅助检查点（在 [Eternal Orchestrations](https://github.com/Azure/durabletask/wiki/Feature---Eternal-Orchestrations-(aka-infinite-loops) 中描述），就无法实现无限循环。）


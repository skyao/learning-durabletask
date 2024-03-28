---
title: "编写任务活动"
linkTitle: "编写任务活动"
weight: 50
date: 2024-01-19
description: >
  在 Durable Task Framework 中编写任务活动
---

> https://github.com/Azure/durabletask/wiki/Writing-Task-Activities

任务活动是协调的 "叶" 节点。这是在协调中实际执行单元操作的代码。这是没有约束的普通 C# 代码。

任务活动代码保证至少被调用一次(at least once)。但在错误情况下，它可能会被调用多次，因此最好是幂等调用。

注：在框架的未来版本中，用户可以将保证切换为最多调用一次，而不是最少调用一次，从而在协调代码中获得更多控制权。

### 备注

这里提到了 at least once 和 at most once 的问题。活动的执行保证还是很麻烦的。
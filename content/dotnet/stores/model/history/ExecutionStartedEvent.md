---
title: "ExecutionStartedEvent事件"
linkTitle: "ExecutionStartedEvent"
weight: 10
date: 2024-01-19
description: >
  核心编程模型的 ExecutionStartedEvent 事件
---



包含属性：

- string EventId
- string Input
- EventType EventType
- ParentInstance ParentInstance
- string Name
- string Version：可以复用
- IDictionary<string, string> Tags
- string Correlation
- DistributedTraceContext ParentTraceContext
- DateTime ScheduledStartTime
- int Generation

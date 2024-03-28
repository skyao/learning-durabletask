---
title: "Task Hub 管理"
linkTitle: "Task Hub 管理"
weight: 80
date: 2024-01-19
description: >
  在 Durable Task Framework 中对 Task Hub 进行管理
---

> https://github.com/Azure/durabletask/wiki/Task-Hub-Management

TaskHubWorker 的 API 可用于对 TaskHub 本身执行 CRUD 操作。

```c#
string serviceBusConnString = "Endpoint=sb://<namespace>.servicebus.windows.net/;SharedSecretIssuer=[issuer];SharedSecretValue=[value]";

string tableConnectionString = "UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://127.0.0.1:10002/";

TaskHubWorker hubWorker = new TaskHubWorker("mytesthub", serviceBusConnString, tableConnectionString);

// creates the required underlying entities in Service Bus and Azure Storage for the task hub
hubWorker.CreateHub();

// creates the required underlying entities in Service Bus and Azure Storage for the task hub
// only if they don't already exist
hubWorker.CreateHubIfNotExists();

// deletes the underlying entities in Service Bus and Azure Storage for the task hub
hubWorker.DeleteHub();

// existence check
bool hubExists = hubWorker.HubExists();
```

Azure 存储连接字符串是可选的。如果不提供该字符串，将无法创建实例存储，因此也就无法查询实例数据。
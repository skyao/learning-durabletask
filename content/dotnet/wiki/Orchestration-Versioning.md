---
title: "编排版本控制"
linkTitle: "编排版本控制"
weight: 90
date: 2024-01-19
description: >
  在 Durable Task Framework 中对编排进行版本控制
---

> https://github.com/Azure/durabletask/wiki/Orchestration-Versioning

本节将概述升级长期运行的协调的过程，并提供示例代码。

使用协调进行调度和工作流管理时，不可避免地会遇到以下挑战：

1. 需要对协调和活动进行更新、版本控制或删除。
2. 新版本的协调可能无法向后兼容。事实上，我们严格假定它们永远不会向后兼容。
3. 在升级时，协调可能正在进行某些工作。我们必须允许它运行到完成，以避免系统处于潜在的不一致状态。另一种方法是以这样一种方式设计协调：删除hub（因此，在中途终止协调）不会使系统处于不一致状态。

处理这些挑战有一套流程，以下是其大纲。

## 过程概要

1. 在调度协调时，我们不使用类名，而是使用字符串名和版本名。这样我们就可以将两个不同的协调类视为同一协调的两个不同版本。

2. 更新协调和/或活动时，我们会将新旧代码都部署到 Worker 上。这需要让之前启动的协调运行完成，然后在运行时优雅地切换到新版本。

3. 我们在 Worker 中以相同的名称注册新旧协调类，但版本不同。我将在示例中介绍这一点，但我们的想法是使用一个类似于 ObjectCreator 工厂的类。

4. 对旧的协调代码稍作修改：新版本中提供了 ContinueAsNew 方法（用于启动新的生成）。例如，当您第一次使用版本 "1" 的协调时，您可以在代码中使用如下内容：

   `Context.ContinueAsNew("1", input);`

   现在，我们将其改为

   `Context.ContinueAsNew("2", input);`

   这样，当旧的协调完成时，它就会使用新版本重新进行调度。在实践中，我不建议直接修改代码；相反，我们应该建立一个包含常量的文件，然后在代码中使用这些常量。当然，我们必须重新构建协调代码，以便获得常量的新值，或者使用属性代替。

5. 应更新客户端以调度新版本的协调（为了保持一致性）

6. 停止和删除协调的过程与更新过程相同，只是新版本是一个立即退出的无操作协调。在下一次部署时，两个版本都可以安全删除。

## 代码

在本节中，我将向您展示我们需要使用哪些方法重载来实现上一小节中概述的过程。

假设我们有两个类，都已部署：`InfiniteOrchestrationV1`和`InfiniteOrchestrationV2`。

同时，让我们给它们起一个字符串名称 "Infinite"，并分别给它们起一个版本 "1" 和 "2"。版本应为字符串，但可解析为 int。

首先，我们在 Worker 中调度两个版本。

我们首先创建两个 `ObjectCreator` 子类型"`TaskHubObjectCreator<TaskOrchestration>`"，它们接受三个参数：

- 编排逻辑名称（在我们的例子中，两个编排都被命名为 "Infinite"）
- 版本（分别为 "1" 和 "2"）
- 为（名称、版本）对实际创建正确协调实例的 Lambda：

```c#
var InfiniteV1 = new TaskHubObjectCreator<TaskOrchestration>("Infinite", "1", () => { return new InfiniteDemoOrchestrationV1(); });

var InfiniteV2 = new TaskHubObjectCreator<TaskOrchestration>("Infinite", "2", () => { return new InfiniteDemoOrchestrationV2(); });
```

下面我将提供 TaskHubObjectCreator 类的定义。然后，我们将实例放入一个数组中：

```c#
var InfiniteOrchestrations = new[] {InfiniteV1, InfiniteV2};
```

最后，我们使用 TaskHubWorker AddTaskOrchestrations 重载，它接受一个 ObjectCreator 实例列表：

```c#
var taskWorkerHub2 = new TaskHubWorker(Constants.HubName, Constants.ServiceBusConnString)
    .AddTaskOrchestrations(InfiniteOrchestrations)
    .AddTaskActivities(typeof(DemoActivityV1), typeof(DemoActivityV2));
```

这样，worker 现在就能知道协调的两个版本，并将它们关联为具有相同名称但不同版本的协调。

以下是 TaskHubObjectCreator 类的定义：使用 DurableTask.TaskHubObjectCreator 类；

```c#
using System;
using DurableTask;

/// <summary>
/// A factory class which allows creation an orchestration instance based on the string ID and version
/// </summary>
public class TaskHubObjectCreator : ObjectCreator<TaskOrchestration>
{
    private readonly Func<TaskOrchestration> objectCreatorFunc;

    /// <summary>
    /// Constructs an instance of TaskHubObjectCreator
    /// </summary>
    /// <param name="name">The string name of the orchestration which this factory creates. Several different classes which are conceptually related can have the same string ID but differnet version.</param>
    /// <param name="version">The version of the orchestration that this object creates</param>
    /// <param name="objectCreatorFunc">Creator function. This function must create the correct object for the (name, version) pair provided</param>
    public TaskHubObjectCreator(string name, string version, Func<TaskOrchestration> objectCreatorFunc)
    {
        if (string.IsNullOrEmpty(name))
        {
            throw new ArgumentNullException("name");
        }

        if (string.IsNullOrEmpty(version))
        {
            throw new ArgumentNullException("version");
        }

        if (objectCreatorFunc == null)
        {
            throw new ArgumentNullException("objectCreatorFunc");
        }

        this.Name = name;
        this.Version = version;
        this.objectCreatorFunc = objectCreatorFunc;
    }

    /// <summary>
    /// Invokes the creator function, thus creating the correct instance for (name, version) provided.
    /// </summary>
    /// <returns>An instance of an orchestration</returns>
    public override TaskOrchestration Create()
    {
        return this.objectCreatorFunc();
    }
}
```

完成 Worker 的配置后，我们需要编写与默认不同的客户端，以使该进程正常运行：
`orchestrationInstance = client.CreateOrchestrationInstance("Infinite", "1", "1", input);`

第一个参数是协调的名称。通常，这将是类型或类型的 .ToString 值。但由于我们用逻辑名称 "Infinite "注册了协调，因此将使用该名称。

第二个参数是版本，等于 "1"。这将是 Worker 可以理解的版本之一。
第三个参数（也是 "1"）是 ID。出于幂等性考虑，ID 在不同调用中应保持一致。
最后一个参数是协调的输入，其类型由泛型决定。

最后，在部署过程中不要忘记用版本号更新常量：一旦部署完成，旧的协调必须继续使用新的版本。


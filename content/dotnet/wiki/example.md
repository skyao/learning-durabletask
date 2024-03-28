---
title: "视频编码的示例"
linkTitle: "示例"
weight: 30
date: 2024-01-19
description: >
  Durable Task Framework 视频编码的示例
---

> https://github.com/Azure/durabletask/wiki/Example---Video-Encoding

假设用户希望建立一个代码协调，对视频进行编码，然后在编码完成后向用户发送电子邮件。

为了使用服务总线持久任务框架实现这一目标，用户将编写两个任务活动，分别用于编码视频和发送电子邮件，以及一个任务协调，在这两个活动之间进行协调。

```c#
public class EncodeVideoOrchestration : TaskOrchestration<string, string>
{
    public override async Task<string> RunTask(OrchestrationContext context, 
                                                 string input)
    {
        string encodedUrl = 
              await context.ScheduleTask<string>(typeof (EncodeActivity), input);
        await context.ScheduleTask<object>(typeof (EmailActivity), input);
        return encodedUrl;
    }
}
```

在此协调中，用户调度编码视频活动，等待响应，然后调度发送电子邮件活动。框架将确保持久保存执行状态。例如，如果托管上述任务协调的节点在调度编码视频活动之前崩溃，那么重启时它就会知道要调度该活动。如果节点在调度完活动后但在收到响应前崩溃，重启时它就会聪明地知道该活动已被调度，并直接开始等待 EncodeVideo 活动的响应。

```c#
public class EncodeActivity : TaskActivity<string, string>
{
    protected override string Execute(TaskContext context, string input)
    {
        Console.WriteLine("Encoding video " + input);
        // TODO : actually encode the video to a destination
        return "http://<azurebloblocation>/encoded_video.avi";
    }
}

public class EmailActivity : TaskActivity<string, object>
{
    protected override object Execute(TaskContext context, string input)
    {
        // TODO : actually send email to user
        return null;
    }
}
```

上述用户代码（EncodeVideoOrchestration、EncodeActivity 和 EmailActivity）需要在某个地方托管和可用才能发挥作用。

这样，用户才能在 Worker 中加载这些协调和活动类，并开始处理请求以创建新的协调实例。

```c#
string serviceBusConnString = "Endpoint=sb://<namespace>.servicebus.windows.net/;SharedSecretIssuer=[issuer];SharedSecretValue=[value]";

TaskHubWorker hubWorker = new TaskHubWorker("myvideohub", serviceBusConnString)
    .AddTaskOrchestrations(typeof (EncodeVideoOrchestration))
    .AddTaskActivities(typeof (EncodeActivity), typeof (EmailActivity))
    .Start();
```

这些 Worker 的多个实例可针对同一个任务中心同时运行，以根据需要提供负载平衡。该框架保证特定的协调实例代码在同一时间只能在单个 Worker 上执行。

TaskHubWorker 还提供了停止 Worker 实例的方法。

最后剩下的部分是创建和管理协调实例，即如何实际触发用户加载的代码协调，以及如何监控或终止它们。

```c#
string serviceBusConnString = "Endpoint=sb://<namespace>.servicebus.windows.net/;SharedSecretIssuer=[issuer];SharedSecretValue=[value]";

TaskHubClient client = new TaskHubClient("myvideohub", serviceBusConnString);
client.CreateOrchestrationInstance(typeof (EncodeVideoOrchestration), "http://<azurebloblocation>/MyVideo.mpg");
```
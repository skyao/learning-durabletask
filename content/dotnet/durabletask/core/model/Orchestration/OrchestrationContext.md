---
title: "OrchestrationContext"
linkTitle: "OrchestrationContext"
weight: 20
date: 2024-01-19
description: >
  核心编程模型之 OrchestrationContext
---



`src\DurableTask.Core\OrchestrationContext.cs`

Context for an orchestration containing the instance, replay status, orchestration methods and proxy methods

包含实例、重放状态、协调方法和代理方法的协调上下文



## 类定义

OrchestrationContext 是一个 抽象类：

```c#
    public abstract class OrchestrationContext
    {
    }
```

实现类有 TaskOrchestrationContext。

OrchestrationContext 定义有以下 (**public** **virtual**) 方法：

- CreateClient()
- CreateClientV2()
- CreateRetryableClient()
- ScheduleWithRetry()
- ScheduleTask()
- CreateTimer()
- CreateSubOrchestrationInstance()
- CreateSubOrchestrationInstanceWithRetry()
- SendEvent()
- ContinueAsNew()



### ScheduleTask()



```c#
        /// <param name="version">Name of the orchestration as specified by the ObjectCreator</param>
public abstract Task<TResult> ScheduleTask<TResult>(string name, string version, params object[] parameters);
```

这里是有 version 概念的。



### ScheduleWithRetry()

```c#
        public virtual Task<T> ScheduleWithRetry<T>(Type taskActivityType, RetryOptions retryOptions,
            params object[] parameters)
        {
            return ScheduleWithRetry<T>(NameVersionHelper.GetDefaultName(taskActivityType),
                NameVersionHelper.GetDefaultVersion(taskActivityType),
                retryOptions, parameters);
        }
```

NameVersionHelper.GetDefaultVersion(taskActivityType) 目前没有实现，只是写死了返回空字符串：

```c
        public static string GetDefaultVersion(object obj)
        {
            return string.Empty;
        }
```

之后调用带 version 参数的重载方法：

```c#
        public virtual Task<T> ScheduleWithRetry<T>(string name, string version, RetryOptions retryOptions,
            params object[] parameters)
        {
            Task<T> RetryCall() => ScheduleTask<T>(name, version, parameters);
            var retryInterceptor = new RetryInterceptor<T>(this, retryOptions, RetryCall);
            return retryInterceptor.Invoke();
        }
```

还是调用到 ScheduleTask() 方法上了。

### CreateTimer()



```c#
        public abstract Task<T> CreateTimer<T>(DateTime fireAt, T state);

        public abstract Task<T> CreateTimer<T>(DateTime fireAt, T state, CancellationToken cancelToken);
```



### CreateSubOrchestrationInstance()



```c#
        public virtual Task<T> CreateSubOrchestrationInstance<T>(Type orchestrationType, object input)
        {
            return CreateSubOrchestrationInstance<T>(NameVersionHelper.GetDefaultName(orchestrationType),
                NameVersionHelper.GetDefaultVersion(orchestrationType), input);
        }

        public virtual Task<T> CreateSubOrchestrationInstance<T>(Type orchestrationType, string instanceId, object input)
        {
            return CreateSubOrchestrationInstance<T>(NameVersionHelper.GetDefaultName(orchestrationType),
                NameVersionHelper.GetDefaultVersion(orchestrationType), instanceId, input);
        }

        public abstract Task<T> CreateSubOrchestrationInstance<T>(string name, string version, object input);

```

这个方法也是定义有 version 参数的，只是依然是没有被使用。

### SendEvent()

```c#
         public abstract void SendEvent(OrchestrationInstance orchestrationInstance, string eventName, object eventData);
```

在 OrchestrationInstance 增加了 version 参数之后，这个方法也有 version 的概念了。

### ContinueAsNew()

```c#
public abstract void ContinueAsNew(object input);
```

没有 version 的概念，最好在实现中重用当前 instance 的 version（如果有指定）。



### CreateClient

*Create a proxy client class to schedule remote TaskActivities via a strongly typed interface.*

创建一个代理客户端类，通过强类型接口调度远程 TaskActivities。



```c#
        public virtual T CreateClient<T>() where T : class
        {
            return CreateClient<T>(false);
        }

        public virtual T CreateClient<T>(bool useFullyQualifiedMethodNames) where T : class
        {
            return CreateClient<T>(() => new ScheduleProxy(this, useFullyQualifiedMethodNames));
        }

				private static T CreateClient<T>(Func<IInterceptor> createScheduleProxy) where T : class
        {
            if (!typeof(T).IsInterface && !typeof(T).IsClass)
            {
                throw new InvalidOperationException($"{nameof(T)} must be an interface or class.");
            }

            IInterceptor scheduleProxy = createScheduleProxy();

            if (typeof(T).IsClass)
            {
                if (typeof(T).IsSealed)
                {
                    throw new InvalidOperationException("Class cannot be sealed.");
                }

                return ProxyGenerator.CreateClassProxy<T>(scheduleProxy);
            }

            return ProxyGenerator.CreateInterfaceProxyWithoutTarget<T>(scheduleProxy);
        }
```

这里没有 version 的概念。














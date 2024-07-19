---
title: "调用堆栈"
linkTitle: "调用堆栈"
weight: 100
date: 2024-01-19
description: >
  DurableTask worker register Orchestration 的调用堆栈
---



## 调用堆栈概况

在 azure-functions-durable-extension 仓库的 `src\WebJobs.Extensions.DurableTask\DurableTaskExtension.cs`文件中，增加堆栈打印的代码：

```c#
        internal void RegisterOrchestrator(FunctionName orchestratorFunction, RegisteredFunctionInfo orchestratorInfo)
        {
          Console.WriteLine("call RegisterOrchestrator!");
          Console.WriteLine("Information of RegisterOrchestrator: \n" + Environment.StackTrace);
           ......
        }
```

得到的输出如下：

```bash
   at Microsoft.Azure.WebJobs.Extensions.DurableTask.DurableTaskExtension.RegisterOrchestrator(FunctionName orchestratorFunction, RegisteredFunctionInfo orchestratorInfo) in C:\Users\sky\work\code\durabletask-fork2\azure-functions-durable-extension\src\WebJobs.Extensions.DurableTask\DurableTaskExtension.cs:line 1215
   at Microsoft.Azure.WebJobs.Extensions.DurableTask.OrchestrationTriggerAttributeBindingProvider.TryCreateAsync(TriggerBindingProviderContext context) in C:\Users\sky\work\code\durabletask-fork2\azure-functions-durable-extension\src\WebJobs.Extensions.DurableTask\Bindings\OrchestrationTriggerAttributeBindingProvider.cs:line 69
   at Microsoft.Azure.WebJobs.Host.Triggers.CompositeTriggerBindingProvider.TryCreateAsync(TriggerBindingProviderContext context) in D:\a\_work\1\s\src\Microsoft.Azure.WebJobs.Host\Triggers\CompositeTriggerBindingProvider.cs:line 20
   at System.Runtime.CompilerServices.AsyncMethodBuilderCore.Start[TStateMachine](TStateMachine& stateMachine)
   at Microsoft.Azure.WebJobs.Host.Triggers.CompositeTriggerBindingProvider.TryCreateAsync(TriggerBindingProviderContext context)
   at Microsoft.Azure.WebJobs.Host.Indexers.FunctionIndexer.IndexMethodAsyncCore(MethodInfo method, IFunctionIndexCollector index, CancellationToken cancellationToken) in D:\a\_work\1\s\src\Microsoft.Azure.WebJobs.Host\Indexers\FunctionIndexer.cs:line 172
   at System.Runtime.CompilerServices.AsyncMethodBuilderCore.Start[TStateMachine](TStateMachine& stateMachine)
   at Microsoft.Azure.WebJobs.Host.Indexers.FunctionIndexer.IndexMethodAsyncCore(MethodInfo method, IFunctionIndexCollector index, CancellationToken cancellationToken)
   at Microsoft.Azure.WebJobs.Host.Indexers.FunctionIndexer.IndexMethodAsync(MethodInfo method, IFunctionIndexCollector index, CancellationToken cancellationToken) in D:\a\_work\1\s\src\Microsoft.Azure.WebJobs.Host\Indexers\FunctionIndexer.cs:line 149
   at System.Runtime.CompilerServices.AsyncMethodBuilderCore.Start[TStateMachine](TStateMachine& stateMachine)
   at Microsoft.Azure.WebJobs.Host.Indexers.FunctionIndexer.IndexMethodAsync(MethodInfo method, IFunctionIndexCollector index, CancellationToken cancellationToken)
   at Microsoft.Azure.WebJobs.Host.Indexers.FunctionIndexer.IndexTypeAsync(Type type, IFunctionIndexCollector index, CancellationToken cancellationToken) in D:\a\_work\1\s\src\Microsoft.Azure.WebJobs.Host\Indexers\FunctionIndexer.cs:line 81
   at System.Runtime.CompilerServices.AsyncMethodBuilderCore.Start[TStateMachine](TStateMachine& stateMachine)
   at Microsoft.Azure.WebJobs.Host.Indexers.FunctionIndexer.IndexTypeAsync(Type type, IFunctionIndexCollector index, CancellationToken cancellationToken)
```



### CompositeTriggerBindingProvider

   at Microsoft.Azure.WebJobs.Host.Triggers.CompositeTriggerBindingProvider.TryCreateAsync(TriggerBindingProviderContext context) in D:\a\_work\1\s\src\Microsoft.Azure.WebJobs.Host\Triggers\CompositeTriggerBindingProvider.cs:line 20



```c#
```





### OrchestrationTriggerAttributeBindingProvider



   at Microsoft.Azure.WebJobs.Extensions.DurableTask.OrchestrationTriggerAttributeBindingProvider.TryCreateAsync(TriggerBindingProviderContext context) in C:\Users\sky\work\code\durabletask-fork2\azure-functions-durable-extension\src\WebJobs.Extensions.DurableTask\Bindings\OrchestrationTriggerAttributeBindingProvider.cs:line 69



```c#
        public Task<ITriggerBinding?> TryCreateAsync(TriggerBindingProviderContext context)
        {
            if (context == null)
            {
                throw new ArgumentNullException(nameof(context));
            }

            ParameterInfo parameter = context.Parameter;
            OrchestrationTriggerAttribute? trigger = parameter.GetCustomAttribute<OrchestrationTriggerAttribute>(inherit: false);
            if (trigger == null)
            {
                return Task.FromResult<ITriggerBinding?>(null);
            }

            // Priority for getting the name is [OrchestrationTrigger], [FunctionName], method name
            string name = trigger.Orchestration;
            if (string.IsNullOrEmpty(name))
            {
                MemberInfo method = context.Parameter.Member;
                name = method.GetCustomAttribute<FunctionNameAttribute>()?.Name ?? method.Name;
            }

            var orchestratorName = new FunctionName(name);
            if (name.StartsWith("@"))
            {
                throw new ArgumentException("Orchestration names must not start with @.");
            }

          // 代码走到这里
            this.config.RegisterOrchestrator(orchestratorName, null);
            var binding = new OrchestrationTriggerBinding(this.config, parameter, orchestratorName, this.connectionName, this.platormInformation);
            return Task.FromResult<ITriggerBinding?>(binding);
        }
```





### DurableTaskExtension

```c#
internal void RegisterOrchestrator(FunctionName orchestratorFunction, RegisteredFunctionInfo orchestratorInfo)
        {
          // print stack trace
          var st = new StackTrace(true);


            if (orchestratorInfo != null)
            {
                orchestratorInfo.IsDeregistered = false;
            }

            if (this.knownOrchestrators.TryAdd(orchestratorFunction, orchestratorInfo))
            {
                this.TraceHelper.ExtensionInformationalEvent(
                    this.Options.HubName,
                    instanceId: string.Empty,
                    functionName: orchestratorFunction.Name,
                    message: $"Registered orchestrator function named {orchestratorFunction}.",
                    writeToUserLogs: false);
            }
            else
            {
                this.knownOrchestrators[orchestratorFunction] = orchestratorInfo;
            }
        }
```


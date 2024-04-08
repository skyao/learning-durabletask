---
title: "OrchestrationTriggerAttribute"
linkTitle: "OrchestrationTriggerAttribute"
weight: 10
date: 2024-01-19
description: >
  OrchestrationTriggerAttribute
---



```c#
    public sealed class OrchestrationTriggerAttribute : Attribute
    {
        /// <summary>
        /// Gets or sets the name of the orchestrator function.
        /// </summary>
        /// <remarks>
        /// If not specified, the function name is used as the name of the orchestration.
        /// </remarks>
        /// <value>
        /// The name of the orchestrator function or <c>null</c> to use the function name.
        /// </value>
#pragma warning disable CS0618 // Type or member is obsolete
        [AutoResolve]
#pragma warning restore CS0618 // Type or member is obsolete
        public string Orchestration { get; set; }
    }
```

### Orchestration() method

Gets or sets the name of the orchestrator function.

> 获取或设置协调器函数的名称。

If not specified, the function name is used as the name of the orchestration.

> 如果未指定，函数名称将用作协调名称。

Value:
 The name of the orchestrator function or `null` to use the function name.

这个方法被 `src\WebJobs.Extensions.DurableTask\Bindings\OrchestrationTriggerAttributeBindingProvider.cs` 的 TryCreateAsync() 方法调用：



```bash
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

            this.config.RegisterOrchestrator(orchestratorName, null);
            var binding = new OrchestrationTriggerBinding(this.config, parameter, orchestratorName, this.connectionName, this.platormInformation);
            return Task.FromResult<ITriggerBinding?>(binding);
        }
```


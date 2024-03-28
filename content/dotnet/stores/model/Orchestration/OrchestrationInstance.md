---
title: "OrchestrationInstance"
linkTitle: "OrchestrationInstance"
weight: 20
date: 2024-01-19
description: >
  核心编程模型之 OrchestrationInstance
---



`src\DurableTask.Core\OrchestrationInstance.cs`

OrchestrationInstance 中定义了几个属性：

- InstanceId
- ExecutionId() 

```c#
    [DataContract]
    public class OrchestrationInstance : IExtensibleDataObject
    {
        /// <summary>
        /// The instance id, assigned as unique to the orchestration
        /// </summary>
        [DataMember]
        public string InstanceId { get; set; }

        /// <summary>
        /// The execution id, unique to the execution of this instance
        /// </summary>
        [DataMember]
        public string ExecutionId { get; set; }
```


---
title: "特性-框架诊断"
linkTitle: "特性-框架诊断"
weight: 160
date: 2024-01-19
description: >
  在 Durable Task Framework 中实现框架诊断的特性
---

> https://github.com/Azure/durabletask/wiki/Framework-Diagnostics

> **重要**： 此信息已过时，仅适用于已废弃的 Durable Task Framework v1 版本。关于所有最新版本，请参阅 [此处](https://github.com/Azure/durabletask/tree/main/src/DurableTask.Core/Logging#readme) 信息。

框架的所有组件都会记录到跟踪源 "DurableTask"。可以将监听器连接到该跟踪源，以获取框架跟踪。

框架随附的 TraceListener 可将日志记录到控制台和调试流。监听器的类名为 DurableTask.Tracing.OrchestrationConsoleTraceListener。

下面是一个 app.config 文件片段，显示了如何加载控制台跟踪监听器：

```c#
  <system.diagnostics>
    <trace autoflush="true"/>
    <sources>
      <source name="DurableTask"
              switchName="traceSwitch"
              switchType="System.Diagnostics.SourceSwitch" >
        <listeners>
          <clear/>
          <add name="configConsoleListener" 
            type=" DurableTask.Tracing.OrchestrationConsoleTraceListener, DurableTask"
               traceOutputOptions="DateTime" />
        </listeners>
      </source>
    </sources>
    <switches>
      <add name="traceSwitch" value="Verbose" />
    </switches>
  </system.diagnostics>
```
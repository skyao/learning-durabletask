---
title: "配置 VS Code 的 remote ssh"
linkTitle: "VS Code"
weight: 100
date: 2024-01-19
description: >
  在 windows 上配置 VS Code 的 remote ssh
---



前面的准备工作中，已经在 windows 上安装了 openssh-server，因此现在可以利用 vs code 的 remote ssh 功能来实现在 windows 上开发代码，但是 vs code 界面可以运行在其他地方，如我的 m1 笔记本。



## 连接 windows 主机

在 vs code 中，通过 "connect to host" 连接到前面准备好的 windows。

第一次会自动下载并准备 vs code 后端。

 

## 打开项目



打开项目时，报错：

```bash
C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.SqlServer.Tests\DurableTask.SqlServer.Tests.csproj : error NU1903: Warning As Error: Package 'System.Data.SqlClient' 4.8.5 has a known high severity vulnerability, https://github.com/advisories/GHSA-98g6-xh36-x2p7 [C:\Users\sky\work\code\durabletask\durabletask\DurableTask.sln]
  Failed to restore C:\Users\sky\work\code\durabletask\durabletask\test\DurableTask.SqlServer.Tests\DurableTask.SqlServer.Tests.csproj (in 1.2 sec).
  23 of 24 projects are up-to-date for restore.
```


---
title: "安装nuget"
linkTitle: "nuget"
weight: 120
date: 2024-01-19
description: >
  在 windows 上安装nuget
---



##    下载

下载地址：

https://www.nuget.org/downloads

- [nuget.exe - recommended latest v6.9.1](https://dist.nuget.org/win-x86-commandline/latest/nuget.exe)

## 安装

将 `nuget.exe` 复制到 `C:\soft\nuget` 目录，然后修改 windows 系统设置中的 path 环境变量，加入地址 `C:\soft\nuget`

## 验证

查看版本：

```bash
$ nuget
NuGet 版本: 6.9.1.3
```


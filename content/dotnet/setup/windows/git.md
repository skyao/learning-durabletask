---
title: "安装git"
linkTitle: "git"
weight: 10
date: 2024-01-19
description: >
  在 windows 上安装 git
---



## 安装 git

下载 git 的 windows 安装包，一路 next 安装即可。

为了避免空格，我将安装路径从默认的 `C:\Program Files\Git`  修改为 `C:\soft\git`。

安装完成之后，除了 git 外，还顺便得到了一个 bash 终端，它至少比 windows 自带的终端要好用。

后面会继续安装 zsh 来替代 bash。



## 配置自动登录

将本地的 id_rsa.pub 公钥传到 windows 机器

```bash
scp id_rsa.pub sky@192.168.0.103:~/.ssh/
```

然后加入到 authorized_keys ，注意由于登录的账号是 administrator 账号，所以 authorized_keys 的文件路径是 "C:\ProgramData\ssh\administrators_authorized_keys" 而不是 "~/.ssh\administrators_authorized_keys"

用管理员身份运行 bash，然后执行命令：

```bash
 touch "C:\ProgramData\ssh\administrators_authorized_keys"
 
 cat ~/.ssh/id_rsa.pub >> "C:\ProgramData\ssh\administrators_authorized_keys"
```

用管理员身份运行 powershell，修改文件属性：

```bash
icacls.exe "C:\ProgramData\ssh\administrators_authorized_keys" /inheritance:r /grant "Administrators:F" /grant "SYSTEM:F"
```

之后就可以在 openssh client 直接 ssh ，不用输入密码。

```bash
ssh sky@192.168.0.103
```

参考:

- https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_server_configuration#authorizedkeysfile


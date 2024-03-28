---
title: "安装 OpenSSH server"
linkTitle: "OpenSSH server"
weight: 40
date: 2024-01-19
description: >
  在 windows 上安装 OpenSSH server
---



参考：

- [Get started with OpenSSH for Windows](https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse?tabs=powershell)



## 使用 powershell 安装

注意：必须以管理员身份运行 powershell。

### 检查是否安装

检查有没有安装 OpenSSH :

```bash
Get-WindowsCapability -Online | Where-Object Name -like 'OpenSSH*'
```

如果没有安装则输出为：

```bash
Name  : OpenSSH.Client~~~~0.0.1.0
State : NotPresent

Name  : OpenSSH.Server~~~~0.0.1.0
State : NotPresent
```

如果已经安装则输出为：

```bash
Name  : OpenSSH.Client~~~~0.0.1.0
State : Installed

Name  : OpenSSH.Server~~~~0.0.1.0
State : Installed
```

### 安装 OpenSSH Client 和 Server

执行安装命令：

```bash
# Install the OpenSSH Client
Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0

# Install the OpenSSH Server
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
```

### 启动  OpenSSH Server

启动 OpenSSH Server 并验证防火墙设置：

```bash
# Start the sshd service
Start-Service sshd

# Confirm the Firewall rule is configured. It should be created automatically by setup. Run the following to verify
if (!(Get-NetFirewallRule -Name "OpenSSH-Server-In-TCP" -ErrorAction SilentlyContinue | Select-Object Name, Enabled)) {
    Write-Output "Firewall Rule 'OpenSSH-Server-In-TCP' does not exist, creating it..."
    New-NetFirewallRule -Name 'OpenSSH-Server-In-TCP' -DisplayName 'OpenSSH Server (sshd)' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22
} else {
    Write-Output "Firewall rule 'OpenSSH-Server-In-TCP' has been created and exists."
}
```

### 设置开机自动启动

```bash
# OPTIONAL but recommended:
Set-Service -Name sshd -StartupType 'Automatic'
```



## 配置 openssh server



### ~~配置默认 shell~~

请在安装好 git (自带bash) 之后再进行这个配置，假定 bash 的安装路径是：`C:\soft\git\usr\bin\bash.exe`

用 powershell 执行命令：

```bash
New-ItemProperty -Path "HKLM:\SOFTWARE\OpenSSH" -Name DefaultShell -Value "C:\soft\git\usr\bin\bash.exe" -PropertyType String -Force
```

如果想把默认shell修改为 zsh，最好是先安装上面的方法将默认 shell 设置为 git 带的 bash，然后再让bash启动zsh，这样就能够利用到 git 带的 bash 里面的一些基本的 linux 命令。如果直接将默认shell改为 zsh，则 ssh 登录之后会报错如下：

```bash
/c/Users/sky/.oh-my-zsh/oh-my-zsh.sh:65: command not found: mkdir
/c/Users/sky/.oh-my-zsh/oh-my-zsh.sh:124: command not found: rm
```

警告：

这个修改完成后，在终端 ssh 上去可以正常工作。但是用 vs code remote ssh 连接上去，就会报错:

```bash
[19:30:31.224] stderr> /usr/bin/bash: line 3: $'\202': command not found
[19:30:31.230] stderr> /usr/bin/bash: line 4: name: command not found
[19:30:31.236] stderr> /usr/bin/bash: line 6: $'\202': command not found
[19:30:31.242] stderr> /usr/bin/bash: line 8: $'\202': command not found
[19:30:31.248] stderr> /usr/bin/bash: line 10: $'\202': command not found
[19:30:31.253] stderr> /usr/bin/bash: line 12: $'\202': command not found
......
[19:31:05.042] stderr> /usr/bin/bash: line 5432: $'\202': command not found
[19:31:05.048] stderr> /usr/bin/bash: line 5434: $'\202': command not found
[19:31:05.054] stderr> /usr/bin/bash: line 5436: $'\202': command not found
[19:31:05.057] Terminating local server
[19:31:05.060] Exec server for ssh-remote+192.168.0.103 failed: Error: Connecting with SSH timed out
```

暂时先回滚这个配置：

```bash
Remove-ItemProperty -Path "HKLM:\SOFTWARE\OpenSSH" -Name DefaultShell -Force
```


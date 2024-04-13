---
title: "项目构建"
linkTitle: "构建"
weight: 10
date: 2024-01-19
description: >
  DurableTask 项目构建
---


参考： https://github.com/microsoft/durabletask-go#building-the-project

## 准备工作

### clone 仓库

```bash
mkdir durabletask
cd durabletask

git clone --recurse-submodules git@github.com:microsoft/durabletask-go.git
cd durabletask-go
```

如果没有使用 `--recurse-submodules` 参数就执行了 clone 操作，则需要：

```bash
git submodule init
git submodule update

# 或者直接
git submodule update --init --recursive
```

这个时候 `submodules/durabletask-protobuf` 目录下就会出现相关的文件。

- submodules/durabletask-protobuf/protos/orchestrator_service.proto

## 构建

### 构建go项目

 ```bash
 go mod tidy
 go mod vendor
 ```

 ### 生成 protobuf 文件

 使用以下命令重新生成子模块的 protobuf。在更新子模块引用时使用该命令。

```bash
 # NOTE: assumes the .proto file defines: option go_package = "/internal/protos"
protoc --go_out=. --go-grpc_out=. -I submodules/durabletask-protobuf/protos orchestrator_service.proto
```

生成的文件会存放在：

- internal/protos/orchestrator_service_grpc.pb.go
- internal/protos/orchestrator_service.pb.go

生成命令执行完成之后，会发现新生成的代码和之前在仓库中提交的代码有很大的不同，这时因为 protoc-gen-go-grpc 和 protoc 版本不同造成的。

仓库中已经提交的版本是：

```bash
// - protoc-gen-go-grpc v1.3.0
// - protoc             v3.12.4
```

而我本地的版本是：

```bash
// - protoc-gen-go-grpc v1.2.0
// - protoc             v3.21.12
```

所以如果真的要生成新的文件并提交，必须使用同样的版本。

## 重新安装 protobuffer 工具

### 重新安装 protoc

```bash
mkdir -p ~/temp
cd ~/temp

wget https://github.com/protocolbuffers/protobuf/releases/download/v3.12.4/protoc-3.12.4-linux-x86_64.zip
unzip protoc-3.12.4-linux-x86_64.zip

sudo cp -r include/google/ /usr/local/include/
sudo chmod -R 755 /usr/local/include/google
sudo cp bin/protoc /usr/local/bin/
sudo chmod +x /usr/local/bin/protoc
```

检验:

```bash
$ protoc --version
libprotoc 3.12.4
```

### 重新安装 protoc-gen-go-grpc

protoc-gen-go-grpc 安装的是 v1.2.0 版本，需要更换为 durabletask 要求的 v1.3.0。执行下面这个命令就可以覆盖之前安装的版本：

```bash
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@v1.3.0
```

### 重新安装 protoc-gen-go 

protoc-gen-go 安装的是 v1.28.1 版本，需要更换为 durabletask 要求的 v1.31.0。执行下面这个命令就可以覆盖之前安装的版本：

```bash
go install google.golang.org/protobuf/cmd/protoc-gen-go@v1.31.0
```

### 小结

重新安装完成 protobuf 相关的工具后，再次执行 protoc 命令生成代码就没有问题：

```bash
protoc --go_out=. --go-grpc_out=. -I submodules/durabletask-protobuf/protos orchestrator_service.proto
```

### 建议

由于 durabletask-go 和 durabletask-java 两个项目都需要用到 protoc 来生成 go 和 java 代码，但是他们两个用的 protoc 版本又不一样，因此最好是搭建两套不同的开发环境，分别安装各自要求的 protoc 版本。

暂时我的实现是开了两个虚拟机，dev101 负责 durabletask-java ， dev102 负责 durabletask-go。


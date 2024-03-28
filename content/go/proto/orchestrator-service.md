---
title: "orchestrator service proto定义"
linkTitle: "orchestrator service"
weight: 10
date: 2024-01-19
description: >
  orchestrator service 的 proto 定义
---

## 背景

`internal/protos/orchestrator_service.pb.go`

proto文件的地址是：

- submodules/durabletask-protobuf/protos/orchestrator_service.proto

生成的文件是：

- internal/protos/orchestrator_service_grpc.pb.go
- internal/protos/orchestrator_service.pb.go

## 定义

proto 定义的 service 名为 TaskHubSidecarService：

```protobuf
service TaskHubSidecarService {
......
}
```

### Hello 方法

```protobuf
    // Sends a hello request to the sidecar service.
    rpc Hello(google.protobuf.Empty) returns (google.protobuf.Empty);
```

### StartInstance 方法

```protobuf
    // Starts a new orchestration instance.
    rpc StartInstance(CreateInstanceRequest) returns (CreateInstanceResponse);
}

CreateInstanceRequest 字段

```protobuf
    message CreateInstanceRequest {
    string instanceId = 1;
    string name = 2;
    google.protobuf.StringValue version = 3;
    google.protobuf.StringValue input = 4;
    google.protobuf.Timestamp scheduledStartTimestamp = 5;
    OrchestrationIdReusePolicy orchestrationIdReusePolicy = 6;
}

TBD：

- version: 这个 version 是做什么的？和版本控制有什么关系？
- input: 为什么是 string 类型？

CreateInstanceResponse 只有一个 instanceId id 字段：

```protobuf
message CreateInstanceResponse {
    string instanceId = 1;
}
```

### GetInstance 方法

```protobuf
    // Gets the status of an existing orchestration instance.
    rpc GetInstance(GetInstanceRequest) returns (GetInstanceResponse);
```

```protobuf
message GetInstanceRequest {
    string instanceId = 1;
    bool getInputsAndOutputs = 2;
}

message GetInstanceResponse {
    bool exists = 1;
    OrchestrationState orchestrationState = 2;
}

message OrchestrationState {
    string instanceId = 1;
    string name = 2;
    google.protobuf.StringValue version = 3;
    OrchestrationStatus orchestrationStatus = 4;
    google.protobuf.Timestamp scheduledStartTimestamp = 5;
    google.protobuf.Timestamp createdTimestamp = 6;
    google.protobuf.Timestamp lastUpdatedTimestamp = 7;
    google.protobuf.StringValue input = 8;
    google.protobuf.StringValue output = 9;
    google.protobuf.StringValue customStatus = 10;
    TaskFailureDetails failureDetails = 11;
}
```





### CreateTaskHub 方法

```protobuf
    // Deletes and Creates the necessary resources for the orchestration service and the instance store
    rpc CreateTaskHub(CreateTaskHubRequest) returns (CreateTaskHubResponse);
```

```protobuf
message CreateTaskHubRequest {
    bool recreateIfExists = 1;
}

message CreateTaskHubResponse {
    //no playload
}
```
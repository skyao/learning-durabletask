---
title: "FunctionRpcClient"
linkTitle: "FunctionRpcClient"
weight: 300
date: 2024-01-19
description: >
  FunctionRpcClient
---



`Azure/azure-functions-dotnet-worker` 仓库下的 FunctionRpcClient protobuf 定义。



## protobuf

proto文件地址：

`protos\azure-functions-language-worker-protobuf\src\proto\FunctionRpc.proto` 



### FunctionRpc service

FunctionRpc.proto  定义了 FunctionRpc 这个 grpc service： 

```protobuf
option java_multiple_files = true;
option java_package = "com.microsoft.azure.functions.rpc.messages";
option java_outer_classname = "FunctionProto";
option csharp_namespace = "Microsoft.Azure.Functions.Worker.Grpc.Messages";
option go_package ="github.com/Azure/azure-functions-go-worker/internal/rpc";

package AzureFunctionsRpcMessages;

import "google/protobuf/duration.proto";
import "identity/ClaimsIdentityRpc.proto";
import "shared/NullableTypes.proto";

// Interface exported by the server.
service FunctionRpc {
}
```

### EventStream 方法

只定义了一个 EventStream 方法：

```protobuf

 rpc EventStream (stream StreamingMessage) returns (stream StreamingMessage) {}

```

request 和 response 都是 stream，类型都是 StreamingMessage。

### StreamingMessage 

StreamingMessage 除了一个 request_id 用来在 host 和 worker 之间做唯一标识外，就只有一个 oneof content 字段：

```protobuf
message StreamingMessage {
  // Used to identify message between host and worker
  string request_id = 1;

  // Payload of the message
  oneof content {
  	......
  }
}
```

消息类型还挺多：

```protobuf
  oneof content {

    // Worker initiates stream
    StartStream start_stream = 20;

    // Host sends capabilities/init data to worker
    WorkerInitRequest worker_init_request = 17;
    // Worker responds after initializing with its capabilities & status
    WorkerInitResponse worker_init_response = 16;

    // MESSAGE NOT USED
    // Worker periodically sends empty heartbeat message to host
    WorkerHeartbeat worker_heartbeat = 15;

    // Host sends terminate message to worker.
    // Worker terminates if it can, otherwise host terminates after a grace period
    WorkerTerminate worker_terminate = 14;

    // Host periodically sends status request to the worker
    WorkerStatusRequest worker_status_request = 12;
    WorkerStatusResponse worker_status_response = 13;

    // On file change event, host sends notification to worker
    FileChangeEventRequest file_change_event_request = 6;

    // Worker requests a desired action (restart worker, reload function)
    WorkerActionResponse worker_action_response = 7;

    // Host sends required metadata to worker to load function
    FunctionLoadRequest function_load_request = 8;
    // Worker responds after loading with the load result
    FunctionLoadResponse function_load_response = 9;

    // Host requests a given invocation
    InvocationRequest invocation_request = 4;

    // Worker responds to a given invocation
    InvocationResponse invocation_response = 5;

    // Host sends cancel message to attempt to cancel an invocation.
    // If an invocation is cancelled, host will receive an invocation response with status cancelled.
    InvocationCancel invocation_cancel = 21;

    // Worker logs a message back to the host
    RpcLog rpc_log = 2;

    FunctionEnvironmentReloadRequest function_environment_reload_request = 25;

    FunctionEnvironmentReloadResponse function_environment_reload_response = 26;

    // Ask the worker to close any open shared memory resources for a given invocation
    CloseSharedMemoryResourcesRequest close_shared_memory_resources_request = 27;
    CloseSharedMemoryResourcesResponse close_shared_memory_resources_response = 28;

    // Worker indexing message types
    FunctionsMetadataRequest functions_metadata_request = 29;
    FunctionMetadataResponse function_metadata_response = 30;

    // Host sends required metadata to worker to load functions
    FunctionLoadRequestCollection function_load_request_collection = 31;

    // Host gets the list of function load responses
    FunctionLoadResponseCollection function_load_response_collection = 32;
    
    // Host sends required metadata to worker to warmup the worker
    WorkerWarmupRequest worker_warmup_request = 33;
    
    // Worker responds after warming up with the warmup result
    WorkerWarmupResponse worker_warmup_response = 34;
    
  }
```

### StartStream



```protobuf
// Worker initiates stream
StartStream start_stream = 20;

// Process.Start required info
//   connection details
//   protocol type
//   protocol version

// Worker sends the host information identifying itself
message StartStream {
  // id of the worker
  string worker_id = 2;
}
```

TBD: 这里可以考虑增加一个 version 字段。



### WorkerInitRequest / WorkerInitResponse



```protobuf
    // Host sends capabilities/init data to worker
    WorkerInitRequest worker_init_request = 17;
    // Worker responds after initializing with its capabilities & status
    WorkerInitResponse worker_init_response = 16;
    

// Host requests the worker to initialize itself
message WorkerInitRequest {
  // version of the host sending init request
  string host_version = 1;

  // A map of host supported features/capabilities
  map<string, string> capabilities = 2;

  // inform worker of supported categories and their levels
  // i.e. Worker = Verbose, Function.MyFunc = None
  map<string, RpcLog.Level> log_categories = 3;

  // Full path of worker.config.json location
  string worker_directory = 4;

  // base directory for function app
  string function_app_directory = 5;
}

// Worker responds with the result of initializing itself
message WorkerInitResponse {
  // PROPERTY NOT USED
  // TODO: Remove from protobuf during next breaking change release
  string worker_version = 1;

  // A map of worker supported features/capabilities
  map<string, string> capabilities = 2;

  // Status of the response
  StatusResult result = 3;

  // Worker metadata captured for telemetry purposes
  WorkerMetadata worker_metadata = 4;
}
```



### WorkerHeartbeat



```protobuf
    // MESSAGE NOT USED
    // Worker periodically sends empty heartbeat message to host
    WorkerHeartbeat worker_heartbeat = 15;
    
    // MESSAGE NOT USED
// TODO: Remove from protobuf during next breaking change release
message WorkerHeartbeat {}
```



### WorkerTerminate



```protobuf
    // Host sends terminate message to worker.
    // Worker terminates if it can, otherwise host terminates after a grace period
    WorkerTerminate worker_terminate = 14;

// Warning before killing the process after grace_period
// Worker self terminates ..no response on this
message WorkerTerminate {
  google.protobuf.Duration grace_period = 1;
}
```





### WorkerStatusRequest / WorkerStatusResponse



```protobuf
    // Host periodically sends status request to the worker
    WorkerStatusRequest worker_status_request = 12;
    WorkerStatusResponse worker_status_response = 13;
    
// Used by the host to determine worker health
message WorkerStatusRequest {
}

// Worker responds with status message
// TODO: Add any worker relevant status to response
message WorkerStatusResponse {
}
```



### InvocationRequest / InvocationResponse





```protobuf
    // Host requests a given invocation
    InvocationRequest invocation_request = 4;

    // Worker responds to a given invocation
    InvocationResponse invocation_response = 5;
    
    
    // Host requests worker to invoke a Function
message InvocationRequest {
  // Unique id for each invocation
  string invocation_id = 1;

  // Unique id for each Function
  string function_id = 2;

  // Input bindings (include trigger)
  repeated ParameterBinding input_data = 3;

  // binding metadata from trigger
  map<string, TypedData> trigger_metadata = 4;

  // Populates activityId, tracestate and tags from host
  RpcTraceContext trace_context = 5;

  // Current retry context
  RetryContext retry_context = 6;
}

// Worker responds with status of Invocation
message InvocationResponse {
  // Unique id for invocation
  string invocation_id = 1;

  // Output binding data
  repeated ParameterBinding output_data = 2;

  // data returned from Function (for $return and triggers with return support)
  TypedData return_value = 4;

  // Status of the invocation (success/failure/canceled)
  StatusResult result = 3;
}
```







### WorkerWarmupRequest / WorkerWarmupResponse



```protobuf
    // Host sends required metadata to worker to warmup the worker
    WorkerWarmupRequest worker_warmup_request = 33;
    
    // Worker responds after warming up with the warmup result
    WorkerWarmupResponse worker_warmup_response = 34;
    
    
    message WorkerWarmupRequest {
  // Full path of worker.config.json location
  string worker_directory = 1;
}

message WorkerWarmupResponse {
  StatusResult result = 1;
}
```




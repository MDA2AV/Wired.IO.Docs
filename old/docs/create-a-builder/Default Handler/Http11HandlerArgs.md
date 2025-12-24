---
title: Http11HandlerArgs
type: docs
sidebar:
  open: false
---

`Http11HandlerArgs` is a configuration record used to initialize the HTTP/1.1 handler in **Wired.IO**.

It allows you to control how HTTP/1.1 requests are handled — including static file serving and processing mode (blocking vs. non-blocking).



## Declaration

```csharp
public record Http11HandlerArgs(
    bool UseResources,
    string ResourcesPath,
    Assembly ResourcesAssembly,
    Http11HandlerType HandlerType = Http11HandlerType.Blocking
) : IHandlerArgs;
```


## Parameters

| Parameter           | Type       | Description |
|---------------------|------------|-------------|
| `UseResources`      | `bool`     | Indicates whether to serve static files from embedded resources. |
| `ResourcesPath`     | `string`   | The virtual path prefix used to locate resources (e.g., `"MyAssemblyName"`). |
| `ResourcesAssembly` | `Assembly` | The assembly containing embedded resources to serve (e.g., `typeof(Program).Assembly`). |
| `HandlerType`       | [`Http11HandlerType`](#http11handlertype) | Defines the handler’s processing mode: `Blocking` or `NonBlocking`. Defaults to `Blocking`. |


## Example

```csharp
var handlerArgs = new Http11HandlerArgs(
    UseResources: true,
    ResourcesPath: "/assets",
    ResourcesAssembly: typeof(Program).Assembly,
    HandlerType: Http11HandlerType.NonBlocking
);
```


# Http11HandlerType

The `Http11HandlerType` enum defines how the HTTP/1.1 handler processes incoming requests over a persistent connection. Defaults to Blocking, the expected behavior for a Http/1.1 as the webserver only accepts new incoming request after finishing the previous one, if NonBlocking is selected the webserver will start handling new requests without waiting for the previous requests to finish, this can lead to issue as the order that responses are written will not be ordered, use at your own caution preferably when response is not important.

## Declaration

```csharp
public enum Http11HandlerType
{
    Blocking,
    NonBlocking
}
```


## Values

| Name          | Description |
|---------------|-------------|
| `Blocking`    | Handles requests one at a time, waiting for each response to complete before reading the next request. Ensures strict ordering and simplicity. |
| `NonBlocking` | Reads and processes pipelined requests concurrently. Improves throughput but requires careful management to preserve response order (if needed). |


## Use Case Comparison

| Feature                      | Blocking        | NonBlocking     |
|------------------------------|------------------|------------------|
| Request concurrency          | ❌ (sequential)  | ✅ (concurrent)   |
| Simplicity                  | ✅               | ❌ (requires async pipeline control) |
| Response order guarantee    | ✅               | ❌ (unless explicitly managed) |
| Best for                    | Simpler systems  | High-throughput, pipelined clients |

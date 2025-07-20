---
title: IContext
type: docs
sidebar:
  open: false
---

`IContext` represents the execution context for a client connection in **Wired.IO**. It encapsulates the request, response, reader/writer streams, cancellation token, and dependency injection scope for the lifetime of a single request or pipelined exchange.


## Declaration

```csharp
public interface IContext : IHasWiredEvents, IDisposable
```


## Properties

### `PipeReader Reader`
Gets or sets the `PipeReader` used to read incoming data from the client connection.

**Remarks:**
> This reader is typically bound to the network stream and used to consume HTTP request data such as headers, body content, or raw bytes from the client.


### `PipeWriter Writer`
Gets or sets the `PipeWriter` used to write outgoing data to the client connection.

**Remarks:**
> This writer is used to serialize the HTTP response, including headers and body, and flush it to the client. It can be wrapped with encoders like chunked or plain writers.


### `IRequest Request`
Gets or sets the HTTP request for the current connection.

**Details:**
> This property contains all the details of the incoming HTTP request, such as the request method, headers, URI, and body.


### `AsyncServiceScope Scope`
Gets or sets the service scope for resolving scoped services during the lifecycle of the request.

**Details:**
> An instance of `AsyncServiceScope` for managing scoped service lifetimes.


### `CancellationToken CancellationToken`
Gets or sets the cancellation token for the current context.

**Remarks:**
> Used to monitor for cancellation requests, allowing graceful termination of operations.  
> Passed to all asynchronous operations initiated within the context.


### `IResponse? Response`
Gets or sets the HTTP response to be sent back to the client.

**Details:**
> This property holds the response data, including status code, headers, and content.  
> It is constructed and written to the stream after the request has been processed.


## Methods

### `void Clear()`
Clears the request and response state of the current context without disposing it.

**Remarks:**
> This method is typically used to reset the context for reuse within a connection handling loop.


## Inherits

- `IHasWiredEvents`
- `IDisposable`


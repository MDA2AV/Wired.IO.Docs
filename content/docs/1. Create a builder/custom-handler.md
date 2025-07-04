---
title: 1.2 Custom Handler
type: docs
next: 2. Create an Endpoint
---

You can roll your own IHttpHandler and injecting it when creating the builder.

#### Create a custom handler

Fulfill the IHttpHandler contract

*Stream stream* : The NetworkStream or SslStream if TLS is enabled.

*Func<TContext, Task> pipeline* : The pipeline that will call the middleware pipeline and route/call the endpoint, when pipeline is invoked, the route should be already set in the TContext.


```csharp
/// <summary>
/// Defines a contract for handling client connections using a custom or HTTP-based protocol.
/// </summary>
public interface IHttpHandler<out TContext>
    where TContext : IContext
{
    /// <summary>
    /// Processes a client connection and dispatches one or more protocol-compliant requests.
    /// </summary>
    /// <param name="stream">The <see cref="Stream"/> representing the client connection.</param>
    /// <param name="pipeline">
    /// A delegate that executes the application's request-handling pipeline, typically consisting of middleware and endpoint logic.
    /// </param>
    /// <param name="stoppingToken">
    /// A <see cref="CancellationToken"/> used to signal cancellation, such as during server shutdown.
    /// </param>
    /// <returns>
    /// A <see cref="Task"/> that represents the asynchronous operation of handling the client session.
    /// </returns>
    Task HandleClientAsync(
        Stream stream,
        Func<TContext, Task> pipeline,
        CancellationToken stoppingToken);
}
```


#### Custom handler pseudo example
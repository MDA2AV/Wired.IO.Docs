---
title: 1.2 Custom Handler
type: docs
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

```csharp
public class CustomContext : IContext
{
    // Implement your custom IContext..
}

public class CustomRequest : IRequest
{
    // Implement your custom IRequest..
}

public class CustomResponse : IResponse
{
    // Implement your custom IResponse..
}

public class CustomHttpHandler<TContext> : IHttpHandler<TContext>
    where TContext : class, IContext, new()
{
    // Pool context objects for less memory pressure (optional)
    private static readonly ObjectPool<TContext> ContextPool =
        new DefaultObjectPool<TContext>(new DefaultPooledObjectPolicy<TContext>(), 8192);

    public async Task HandleClientAsync(Stream stream, Func<TContext, Task> pipeline, CancellationToken stoppingToken)
    {
        // Get a context object from pool (or create a new instance if not pooling)
        var context = ContextPool.Get();

        // Create a new IHttpRequest or use pooling
        context.Request = new CustomRequest();

        // Set up the PipeReader and PipeWriter for the context.
        // You can also skip this and use the Stream directly to read and write from socket.
        // However, the preferred way is to use PipeReader and PipeWriter for better performance.
        // Also, if you decide to use Stream, you will need to cast the IContext passed to the endpoint
        // since IContext does not expose the Stream directly.
        context.Reader = PipeReader.Create(stream,
            new StreamPipeReaderOptions(MemoryPool<byte>.Shared, leaveOpen: true, bufferSize: 8192));

        context.Writer = PipeWriter.Create(stream,
            new StreamPipeWriterOptions(MemoryPool<byte>.Shared, leaveOpen: true));



        // The next section is typically wrapped in a loop or equivalent if the connection is persistent (keep-alive).

        // Read the received request headers and set the context's HttpMethod and Route

        // Call the pipeline callback, it will trigger the middleware pipeline and the endpoint

        // Handle Keep-Alive connections

        // Make sure to dispose managed resources and return the context to the pool


    }
}
```

#### Injecting the custom handler in the builder

Use the handler factory overload to pass the custom handler construction delegate to the CreateBuilder method.

```csharp
var builder = App.CreateBuilder<CustomHttpHandler<CustomContext>, CustomContext>(() => 
    new CustomHttpHandler<CustomContext>());
```

Optionally, also pass the accepted SslAplicationProtocols

```csharp
var builder = App.CreateBuilder<CustomHttpHandler<CustomContext>, CustomContext>(() =>
    new CustomHttpHandler<CustomContext>(), [SslApplicationProtocol.Http11]);
```
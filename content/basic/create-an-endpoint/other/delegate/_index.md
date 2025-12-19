---
title: Passing a Delegate
---

### Pass a delegate to write the response body
If you only want to manually take care of the response body it possible to pass delegate just for the body/content part.

*Keep in mind that this method is likely to create closures.*

```csharp
using System.Buffers;
using Wired.IO.App;

var builder = WiredApp
    .CreateExpressBuilder()
    .Port(8080);

builder
    .MapGroup("/")
    .MapGet("/my-endpoint", context =>
    {
        context
            .Respond()
            .Type("application/octet-stream"u8)
            .Content(() =>
            {
                context.Writer.Write("My endpoint!"u8);
            }, 12);
    });
    
await builder
    .Build()
    .RunAsync();
```

For unknown body/content length, chunked pipe writer must be used instead.

```csharp
using Wired.IO.App;
using Wired.IO.Protocol.Writers;

var builder = WiredApp
    .CreateExpressBuilder()
    .Port(8080);

builder
    .MapGroup("/")
    .MapGet("/my-endpoint", context =>
    {
        context
            .Respond()
            .Type("application/octet-stream"u8)
            .Content(() =>
            {
                var chunkedWriter = new ChunkedWriter();
                chunkedWriter.SetOutput(context.Writer);
                chunkedWriter.Write("My endpoint!"u8);
                chunkedWriter.Complete();
            });
    });
    
await builder
    .Build()
    .RunAsync();
```
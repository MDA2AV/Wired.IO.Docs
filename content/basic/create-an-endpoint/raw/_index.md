---
title: Return Raw Bytes
---

##### Setting the response object and let the framework take care of the HTTP protocol
```csharp
using Wired.IO.App;
using Wired.IO.Http11Express.Response.Content;
using Wired.IO.Protocol.Response;

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
            .Content(new ExpressRawContent("My endpoint!"u8.ToArray()));
    });
    
await builder
    .Build()
    .RunAsync();
```
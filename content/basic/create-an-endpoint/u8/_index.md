---
title: Return a Utf8 Literal
---

##### Setting the response object and let the framework take care of the HTTP protocol
```csharp
using Wired.IO.App;
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
            .Status(ResponseStatus.Ok)
            .Type("text/plain"u8)
            .Content("My endpoint!"u8);
    });
    
await builder
    .Build()
    .RunAsync();
```
---
title: Return a String
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
            .Status(ResponseStatus.Ok)
            .Type("text/plain"u8)
            .Content(new ExpressStringContent("My endpoint!"));
    });
    
await builder
    .Build()
    .RunAsync();
```

##### Writing the HTTP response directly
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
        context.Writer.Write("HTTP/1.1 200 OK\r\n"u8 +
                             "Content-Type: text/plain\r\n"u8 +
                             "Content-Length: 12\r\n\r\n"u8 +
                             "My endpoint!\r\n"u8);
    });
    
await builder
    .Build()
    .RunAsync();
```
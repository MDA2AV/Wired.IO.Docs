---
title: Return a Json Object
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
            .Type("application/json"u8)
            .Content(new ExpressJsonContent(new { Message = "My endpoint!" }));
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
                             "Content-Type: application/json; charset=UTF-8\r\n"u8 +
                             "Content-Length: 26\r\n\r\n"u8 +
                             "{\"Message\":\"My endpoint!\"}\r\n"u8);
    });
    
await builder
    .Build()
    .RunAsync();
```
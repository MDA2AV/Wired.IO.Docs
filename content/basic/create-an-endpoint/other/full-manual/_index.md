---
title: Manually 
---

Write yourself the whole response.

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
---
title: 2.1 Fast Endpoints
type: docs
next: docs/middleware/
---

*Note: In this section we will use the default inbuilt HTTP/1.1 protocol handler.*

## Create a Fast Endpoint


```csharp
using Wired.IO.App;
using Wired.IO.Http11.Context;

var builder = App.CreateBuilder(); // Create a default builder, assumes HTTP/1.1

var app = builder
    .Port(5000) // Configured to http://localhost:5000
    .MapGet("/quick-start", scope => async httpContext =>
    {
        // Your endpoint logic here

        // Manually respond a 200 OK with no content
        await httpContext
            .SendAsync("HTTP/1.1 200 OK\r\nContent-Length:0\r\nContent-Type: application/json\r\nConnection: keep-alive\r\n\r\n"u8.ToArray());
    })
    .Build();

await app.RunAsync();
```
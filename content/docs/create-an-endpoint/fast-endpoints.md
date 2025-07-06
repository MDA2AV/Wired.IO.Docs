---
title: 2.1 Fast Endpoints
type: docs/
---

*Note: In this section we will use the default inbuilt HTTP/1.1 protocol handler.*

### Create a Fast Endpoint


```csharp
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

You can also use the PipeWriter directly

```csharp
.MapGet("/quick-start", scope => async httpContext =>
{
    await httpContext
        .Writer.WriteAsync("HTTP/1.1 200 OK\r\nContent-Length:0\r\nContent-Type: application/json\r\nConnection: keep-alive\r\n\r\n"u8.ToArray());
})
```

WriteAsync will Write() and FlushAsync(), use Write() if you need to write multiple times and flush at the end.

Write() is sync and accepts ReadOnlySpan<T>

```csharp
.MapGet("/quick-start", scope => async httpContext =>
{
    httpContext
        .Writer.Write("HTTP/1.1 200 OK\r\n"u8);
    httpContext
        .Writer.Write("Content-Length:0\r\n"u8);
    httpContext
        .Writer.Write("Content-Type: application/json\r\nConnection: keep-alive\r\n\r\n"u8);

    await httpContext.Writer.FlushAsync();
})
```

Alternatively, use the inbuilt handler's middleware to process the http response automatically.

```csharp
var builder = App.CreateBuilder(); // Create a default builder, assumes HTTP/1.1

var app = builder
    .Port(5000) // Configured to http://localhost:5000
    .MapGet("/quick-start", scope => async httpContext =>
    {
        // Your endpoint logic here

        httpContext
            .Respond()
            .Status(ResponseStatus.Ok)
            .Type("application/json")
            .Content(new JsonContent(
                new { Name = "Toni", Age = 18 }, 
                JsonSerializerOptions.Default));
    })
    .Build();

await app.RunAsync();
```
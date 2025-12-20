---
title: Create an Endpoint
weight: 2
---

Wire up a basic Wired webserver with a GET endpoint.

Wired provides a response building fluent API, yet the framework intention is for the users to manually build their HTTP responses.

```csharp
var builder = WiredApp
    .CreateExpressBuilder() // Use Http11Express, the default Http11 compliant handler
    .Port(8080)
    // Endpoint goes here
    ( ... )

    await builder
    .Build()
    .RunAsync();
```

### Synchronous or Asynchronous

Wired.IO supports both sync or asynchronous endpoints and middleware.

### Synchronous
```csharp
( ... )
.MapGet("/my-endpoint", context =>
{
    context.Writer.Write("HTTP/1.1 200 OK\r\n"u8 +
                            "Content-Type: text/plain\r\n"u8 +
                            "Content-Length: 12\r\n\r\n"u8 +
                            "My endpoint!\r\n"u8);
});
( ... )
```

### Asynchronous
```csharp
( ... )
.MapGet("/my-endpoint", async context =>
{
    await Task.Delay(500); // Simulate asynchronous work

    context.Writer.Write("HTTP/1.1 200 OK\r\n"u8 +
                            "Content-Type: text/plain\r\n"u8 +
                            "Content-Length: 12\r\n\r\n"u8 +
                            "My endpoint!\r\n"u8);
});
( ... )
```
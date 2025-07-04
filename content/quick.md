---
title: ⚡ Let's get you wired up!
type: quick
---

This section contains quick basic examples to get you started, for advanced usage such as configuring TLS, use websockets, add middleware, etc, please check the Docs.

## Include the Wired.IO package in your project.

```bash
dotnet add package Wired.IO --version 9.0.0
```

## Wire up a basic endpoint

No middlewares, directly respond to the socket's NetworkStream using PipeWriter.

```csharp
using Wired.IO.App;
using Wired.IO.Http11.Context;

var builder = App.CreateBuilder(); // Create a default builder, assumes HTTP/1.1

var app = builder
    .Port(5000) // Configured to http://localhost:5000
    .MapGet("/quick-start", scope => async httpContext =>
    {
        await httpContext
            .SendAsync("HTTP/1.1 200 OK\r\nContent-Length:0\r\nContent-Type: application/json\r\nConnection: keep-alive\r\n\r\n"u8.ToArray());
    })
    .Build();

await app.RunAsync();
```

Using response building middleware to correctly send proper response headers and content

```csharp
using System.Text.Json;
using Wired.IO.App;
using Wired.IO.Http11.Response.Content;
using Wired.IO.Protocol.Response;

var builder = App.CreateBuilder(); // Create a default builder, assumes HTTP/1.1

var app = builder
    .Port(5000) // Configured to http://localhost:5000
    .MapGet("/quick-start", scope => httpContext =>
    {
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

## Add logging and inject a dependency

Just like ASP.NET, scoped dependencies are disposed by the end of the request processing.

```csharp
using System.Text.Json;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using Wired.IO.App;
using Wired.IO.Http11.Response.Content;
using Wired.IO.Protocol.Response;

var builder = App.CreateBuilder(); // Create a default builder, assumes HTTP/1.1

builder.App.HostBuilder
    .ConfigureLogging(logging =>
    {
        logging.ClearProviders();
        logging.AddConsole();
        logging.SetMinimumLevel(LogLevel.Information); // Set the minimum log level
    })
    .ConfigureServices((_, services) =>
    {
        services.AddScoped<DependencyService>();
    });

var app = builder
    .Port(5000) // Configured to http://localhost:5000
    .MapGet("/quick-start", scope => async httpContext =>
    {
        var dependency = scope.GetRequiredService<DependencyService>();
        dependency.Handle(); // Use the service

        httpContext
            .Respond()
            .Status(ResponseStatus.Ok)
            .Type("application/json")
            .Content(new JsonContent(
                new { Name = "Alice", Age = 30 }, 
                JsonSerializerOptions.Default));
    })
    .Build();

await app.RunAsync();

class DependencyService(ILogger<DependencyService> logger) : IDisposable
{
    public void Handle() =>
        logger.LogInformation($"{nameof(DependencyService)} was handled.");
    public void Dispose() =>
        logger.LogInformation($"{nameof(DependencyService)} was disposed.");
}
```
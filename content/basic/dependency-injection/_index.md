---
title: Dependency Injection
weight: 2
---

Wired provides first class support for dependency injection through IServiceCollection and IServiceProvider, just like ASP.NET Core.

Registered services can be resolved directly from the context object, this can be achieve both in any endpoint or middleware logic.

Wired also provides configuration to use AsyncServiceScope (Scoped endpoints and middleware resolution) by default.

```csharp
using Microsoft.Extensions.DependencyInjection;
using Wired.IO.App;

var builder = WiredApp
    .CreateExpressBuilder()
    .Port(8080);

builder
    .MapGroup("/")
    .MapGet("/my-endpoint", async context =>
    {
        var service = context.Services.GetRequiredService<ServiceExample>();
        await service.HandleAsync();
        
        context
            .Respond()
            .Type("application/octet-stream"u8)
            .Content("Ok"u8);
    });

builder.Services.AddScoped<ServiceExample>();
    
var app = builder.Build();
await app.RunAsync();


public class ServiceExample : IDisposable
{
    public async Task HandleAsync()
    {
        Console.WriteLine("Handling Service..");
        await Task.Delay(10);
    }

    public void Dispose()
    {
        Console.WriteLine("Service disposed");
    }
}
```

### Disable AsyncServiceScope

For performance reasons this can be disabled, use with caution.

```csharp
( ... )
var builder = WiredApp
    .CreateExpressBuilder()
    .Port(8080)
    .NoScopedEndpoints()
    ( ... )
```
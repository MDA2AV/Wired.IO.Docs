---
title: Logging
weight: 4
---

Wired.IO implements ILoggingFactory with its default parameters

```csharp
    private static void DefaultLoggingBuilder(ILoggingBuilder loggingBuilder)
    {
        loggingBuilder
            .ClearProviders()
#if DEBUG
            .SetMinimumLevel(LogLevel.Debug)
#else
            .SetMinimumLevel(LogLevel.Information)
#endif
            .AddConsole();
    }
```

If you want to override this ILoggingFactory, register yours with your logging builder configuration (Microsoft.Extensions.Logging).

```csharp
( ... )
builder.Services
    .AddLogging(loggingBuilder =>
    {
        // Configure ILoggingBuilder
    })
( ... )
```

Check official docs at 
[ILoggingBuilder docs](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.logging.iloggingbuilder?view=net-9.0-pp)
to explore configurability.


Resolve a ILogger<> anywhere directly from context object.

```csharp
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Wired.IO.App;

var builder = WiredApp
    .CreateExpressBuilder()
    .Port(8080)
    .NoScopedEndpoints();

    builder
        .MapGroup("/api")
        .MapGet("/my-endpoint", context =>
        {
            var logger = context.Services.GetRequiredService<ILogger<Program>>();
            logger.LogInformation("Hello World!");
        
            context
                .Respond()
                .Type("application/octet-stream"u8)
                .Content("Ok"u8);
        });
    
var app = builder.Build();
await app.RunAsync();
```



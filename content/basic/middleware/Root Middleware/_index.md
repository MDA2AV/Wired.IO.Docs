---
title: Root Middleware
---

Root middleware applies to all endpoints.

```csharp
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Wired.IO.App;
using Wired.IO.Http11Express.Response.Content;
using Wired.IO.Protocol.Response;

var builder = WiredApp
    .CreateExpressBuilder()
    .Port(8080)
    .UseRootMiddleware(async (context, next) =>
    {
        // logger or any dependencies can be resolved using scope
        var logger = context.Services.GetRequiredService<ILogger<Program>>();

        try
        {
            // Execute next in line, could be another middleware or the endpoint
            await next(context);
        }
        
        catch (Exception e)
        {
            logger.LogError(e.Message);

            context.Respond()
                .Status(ResponseStatus.InternalServerError)
                .Type("application/json"u8)
                .Content(new ExpressJsonObjectContent(new { Error = e.Message }));
        }
    });

builder
    .MapGroup("/").MapGet("/my-endpoint", context =>
    {
        throw new InvalidOperationException("This shouldn't happen");

        context
            .Respond()
            .Type("application/octet-stream"u8)
            .Content("Ok"u8);
    });
    
var app = builder.Build();
await app.RunAsync();
```
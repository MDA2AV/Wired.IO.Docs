---
title: Group Middleware
---

Group middleware only affects endpoints and other middleware that live in the same group or subgroups.

```csharp
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Wired.IO.App;
using Wired.IO.Http11Express.Response.Content;
using Wired.IO.Protocol.Response;

var builder = WiredApp
    .CreateExpressBuilder()
    .Port(8080)
    .NoScopedEndpoints();

    builder
        .MapGroup("/api")
        .MapGet("/my-endpoint", context =>
        {
            throw new InvalidOperationException("This shouldn't happen");
        
            context
                .Respond()
                .Type("application/octet-stream"u8)
                .Content("Ok"u8);
        })
        .UseMiddleware(async (context, next) =>
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
    
var app = builder.Build();
await app.RunAsync();
```
---
title: Manual Pipeline
weight: 2
---

Instead of creating group layouts (.MapGroup), you can define a custom specific pipeline with its own middlewares.

Manual pipelines allow a base route or prefix which can contain a wildcard for flexibility, multiple Http methods, an endpoint and multiple middlewares.

```csharp
using Wired.IO.App;
using Wired.IO.Http11Express.Context;

async Task MiddlewareExample(Http11ExpressContext ctx, Func<Http11ExpressContext, Task> next)
{
    Console.WriteLine("Executing Manual Pipeline Middleware");
    await next(ctx);
}

await WiredApp
    .CreateExpressBuilder()
    .Port(8080)
    .AddManualPipeline(
    "/api*",
    [HttpConstants.Get, HttpConstants.Post, HttpConstants.Delete, HttpConstants.Put],
    ctx =>
    {
        ctx
            .Respond()
            .Type("text/plain"u8)
            .Content("Hello from manual pipeline!"u8);

        return Task.CompletedTask;
    }, [MiddlewareExample]) 
    .Build()
    .RunAsync();
```
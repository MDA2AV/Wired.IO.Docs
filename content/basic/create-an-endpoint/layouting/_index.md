---
title: Layouting
weight: 1
---

Wired.IO provides multiple options to layout your API.

## Groups

The most common and preferred method is by using group mapping. It is possible to create groups and subgroups, middlware added to a group will affect that group and its subgroups.

```csharp
using System.Buffers;
using Wired.IO.App;
using Wired.IO.Http11Express.Context;

async Task MiddlewareExample(Http11ExpressContext ctx, Func<Http11ExpressContext, Task> next)
{
    Console.WriteLine("Executing Manual Pipeline Middleware");
    await next(ctx);
}

var builder = WiredApp
    .CreateExpressBuilder()
    .Port(8080);

var group1 = builder
    .MapGroup("/group1")
    .UseMiddleware(MiddlewareExample)
    .MapGet("/endpoint1", context =>
    {
        // Endpoint logic here
    })
    .MapGroup("/subgroup1")
    .MapGet("/endpoint2", context =>
    {
        // Endpoint logic here
    });

var group2 = builder
    .MapGroup("/group2")
    .MapGet("/endpoint3", context =>
    {
        // Endpoint logic here
    });

await builder
    .Build()
    .RunAsync();
```

## Root Endpoints

For simpler applications that do not need layouting, use root endpoints.

**Note: Cannot use both group and root endpoints in the same WiredApp.**

```csharp
using System.Buffers;
using Wired.IO.App;

await WiredApp
    .CreateExpressBuilder()
    .Port(8080)
    .UseRootEndpoints()
    .MapGet("/my-endpoint", context =>
    {
        // Endpoint logic here
    })
    .Build()
    .RunAsync();
```

## Manual Pipelines

See Advanced -> Manual Pipelines section
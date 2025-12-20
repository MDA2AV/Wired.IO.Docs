---
title: Routing
weight: 2
---

A few considerations when routing your endpoints.

Wired.IO supports route parameters and wild cards.

### Route Parameters

To access the route parameter value see section

Basic -> Create an endpoint -> Reading Request Data

```csharp
using System.Buffers;
using Wired.IO.App;

var builder = WiredApp
    .CreateExpressBuilder()
    .Port(8080);

builder
    .MapGroup("/")
    .MapGet("/my-endpoint/:param/", context =>
    {
        // Endpoint logic here
    });
    
await builder
    .Build()
    .RunAsync();
```

### Wild Cards

You can use wild cards in your endpoint and manual pipeline routes, it is NOT reccomended to use it in the group route.

```csharp
using System.Buffers;
using Wired.IO.App;

var builder = WiredApp
    .CreateExpressBuilder()
    .Port(8080);

builder
    .MapGroup("/")
    .MapGet("/my-endpoint*", context =>
    {
        // Endpoint logic here
    });
    
await builder
    .Build()
    .RunAsync();
```

In this example any of the following requests will be routed to this endpoint:
 - curl http://localhost:8080/my-endpoint
 - curl http://localhost:8080/my-endpoint/bananas
 - curl http://localhost:8080/my-endpoint//_-1234x1
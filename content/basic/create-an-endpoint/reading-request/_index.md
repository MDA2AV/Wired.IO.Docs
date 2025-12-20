---
title: Reading Request Data
---

To read/consume the HTTP request data, access the context Request object.

```csharp
using Wired.IO.App;

var builder = WiredApp
    .CreateExpressBuilder()
    .Port(8080);

builder
    .MapGroup("/")
    .MapGet("/my-endpoint", context =>
    {
        var route = context.Request.Route;
        var queryParameters = context.Request.QueryParameters;
        var headers = context.Request.Headers;
        var body = context.Request.Content;
        var bodyAsString = context.Request.ContentAsString;
        var bodyLength = context.Request.ContentLength;
        
        context
            .Respond()
            .Type("application/octet-stream"u8)
            .Content("Ok"u8);
    });
    
var app = builder.Build();
await app.RunAsync();
```

### Route Parameters

Wired.IO supports routing with route parameters, there parameters can be extracted from the route.

```csharp
using Wired.IO.App;

var builder = WiredApp
    .CreateExpressBuilder()
    .Port(8080);

builder
    .MapGroup("/")
    
    // For example
    // curl http://localhost:8080/my-endpoint/user/42
    // user = 42
    .MapGet("/my-endpoint/user/:id", context =>
    {
        // You can extract the route parameters from the route
        var route = context.Request.Route;
        var user = route.Split('/').Last();
        
        context
            .Respond()
            .Type("application/octet-stream"u8)
            .Content("Ok"u8);
    });
    
var app = builder.Build();
await app.RunAsync();
```


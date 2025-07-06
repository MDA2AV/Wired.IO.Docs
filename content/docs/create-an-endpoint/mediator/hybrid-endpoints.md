---
title: 2.2.1 - Hybrid Endpoints
type: docs
---

*Note: In this section we will use the default inbuilt HTTP/1.1 protocol handler.*

Hybrid endpoints are a mix between fast and mediator endpoints, ideally if you want to define your endpoint logic in a different class while still using fast endpoints.

```csharp
( ... )
var app = builder

    // Call this to tell the builder to scan assembly and 
    // register your IRequestHandler types
    .AddHandlers(Assembly.GetExecutingAssembly()) 

    .Port(5000) // Configured to http://localhost:5000
    .MapGet("/hybrid-endpoint", scope => async context =>
    {
        // Resolve your IRequestHandler
        var requestHandler = scope
            .GetRequiredService<IRequestHandler<RequestQuery, RequestResult>>();

        // Execute it
        var result = await requestHandler
            .Handle(new RequestQuery(), context.CancellationToken);

        // Pass its result top be processed by the response middleware,
        // optionally deal with the response yourself without calling .Respond()
        context
            .Respond()
            .Status(ResponseStatus.Ok)
            .Type("application/json")
            .Content(new JsonContent(result, JsonSerializerOptions.Default));
    })
( ... )

public class RequestHandlerExample : IRequestHandler<RequestQuery, RequestResult>
{
    public async Task<RequestResult> Handle(RequestQuery query, CancellationToken cancellationToken)
    {
        await Task.Delay(0, cancellationToken); // Do work

        return new RequestResult("Toni", "Mars");
    }
}
public record RequestQuery() : IRequest<RequestResult>;

public record RequestResult(string Name, string Address);
```

The fast endpoint is created to resolve the IRequestHandler.

The IRequestHandler does not know about http method, route or IContext in general, it is abstracted away to deal with business logic.

Just like your typical mediator pattern, it takes in a Query/Command.


#### Void return overload IRequestHandler<TRequest>

Just an overload when request handlers dont need to return anything.

```csharp
( ... )
var app = builder

    // Call this to tell the builder to scan assembly and 
    // register your IRequestHandler types
    .AddHandlers(Assembly.GetExecutingAssembly()) 

    .Port(5000) // Configured to http://localhost:5000
    .MapGet("/hybrid-endpoint", scope => async context =>
    {
        // Resolve your IRequestHandler
        var requestHandler = scope
            .GetRequiredService<IRequestHandler<RequestQuery>>();

        // Execute it
        await requestHandler
            .Handle(new RequestQuery(), context.CancellationToken);

        // NoContent response
        context
            .Respond()
            .Status(ResponseStatus.NoContent);
    })
( ... )

public class RequestHandlerExample : IRequestHandler<RequestQuery>
{
    public async Task Handle(RequestQuery query, CancellationToken cancellationToken)
    {
        await Task.Delay(0, cancellationToken); // Do work
    }
}
public record RequestQuery() : IRequest;
```
---
title: 4.1 Hybrid Endpoints
type: docs
sidebar:
  open: false
---

### Adding IPipelineBehavior for Hybrid Enpdoints

In hybrid endpoints resolve IRequestDispatcher instead of IRequestHandler.

```csharp
( ... )
builder.Services
    // Register the IPipelineBehavior<,>
    .AddScoped(typeof(IPipelineBehavior<,>), typeof(ExampleBehavior<,>))
    .MapGet("/hybrid-endpoint", scope => async context =>
    {
        // Does not execute behaviors
        //var requestHandler = scope.GetRequiredService<IRequestHandler<RequestQuery, RequestResult>>();
        //var result = await requestHandler.Handle(new RequestQuery(), context.CancellationToken);

        // Executes behaviors
        var dispatcher = scope.GetRequiredService<IRequestDispatcher<Http11Context>>(); // Use the corresponding IContext
        var result = await dispatcher.Send(new RequestQuery());

        context
            .Respond()
            .Status(ResponseStatus.Ok)
            .Type("application/json")
            .Content(new JsonContent(result, JsonSerializerOptions.Default));
    })
( ... )

// Define the IPipelineBehavior<TRequest, TResponse>
public class ExampleBehavior<TRequest, TResponse> : IPipelineBehavior<TRequest, TResponse> 
    where TRequest : notnull
{
    public async Task<TResponse> Handle(TRequest request, RequestHandlerDelegate<TResponse> next, CancellationToken cancellationToken)
    {
        // Execute pre endpoint behavior logic here

        // Execute next IPipelineBehavior or IRequestHandler
        return await next();

        // Execute post endpoint behavior logic here
    }
}
```

#### Void response

IPipelineBehavior also supports void response just like IRequestHandler

```csharp
( ... )
builder.Services
    .AddScoped(typeof(IPipelineBehaviorNoResponse<>), typeof(ExampleBehavior<>))
    .MapGet("/hybrid-endpoint", scope => async context =>
    {
        var dispatcher = scope.GetRequiredService<IRequestDispatcher<Http11Context>>();
        await dispatcher.Send(new RequestQuery(), context.CancellationToken);

        context
            .Respond()
            .Status(ResponseStatus.Ok);
    })
( ... )

public class ExampleBehavior<TRequest> : IPipelineBehaviorNoResponse<TRequest>
    where TRequest : IRequest
{
    public async Task Handle(TRequest request, RequestHandlerDelegate next, CancellationToken cancellationToken)
    {
        // Execute pre endpoint behavior logic here
        
        // Execute next IPipelineBehavior or IRequestHandler
        await next();

        // Execute post endpoint behavior logic here
    }
}

public record RequestQuery() : IRequest;
```

#### Register a IPipelineBehavior for specific endpoints

In the previous example we are registering a pipeline behavior for any type, in the following example a void response IRequestHandler will be used but this applies for both IRequestHandler<> and IRequestHandler<,>.

```csharp
.AddScoped(typeof(IPipelineBehavior<,>), typeof(ExampleBehavior<,>)
```

By defining the TRequest, we can filter which endpoints execute the pipeline behavior.

```csharp
( ... )
builder.Services
    .AddScoped(typeof(IPipelineBehaviorNoResponse<RequestQuery>), typeof(ExampleBehavior<RequestQuery>))
    .MapGet("/hybrid-endpoint", scope => async context =>
    {
        var dispatcher = scope.GetRequiredService<IRequestDispatcher<Http11Context>>();
        // Sends for RequestQuery TRequest
        await dispatcher.Send(new RequestQuery(), context.CancellationToken);

        context
            .Respond()
            .Status(ResponseStatus.Ok);
    })
( ... )

public class RequestHandlerExample : IRequestHandler<RequestQuery>
{
    public async Task Handle(RequestQuery request, CancellationToken cancellationToken)
    {
        await Task.Delay(0, cancellationToken); // Do work
    }
}
public record RequestQuery() : IRequest;

public class ExampleBehavior<TRequest> : IPipelineBehaviorNoResponse<RequestQuery>
    where TRequest : IRequest
{
    public async Task Handle(RequestQuery request, RequestHandlerDelegate next, CancellationToken cancellationToken)
    {
        // Execute pre endpoint behavior logic here

        await next();

        // Execute post endpoint behavior logic here
    }
}

```
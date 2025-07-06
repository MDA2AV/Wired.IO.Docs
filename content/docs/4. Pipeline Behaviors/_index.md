---
title: 4. Pipeline Behaviors
type: docs
sidebar:
  open: false
---

While not the most useful feature as it does not provide anything extra apart from maybe slight better code structure, pipeline behaviors can be attached to the mediator pipeline.

When using hybrid or mediator endpoints it is possible to add IPipelineBehavior behaviors that will execute before or after the endpoint. 

In Wired.IO pipeline behaviors are very similar to middleware, the major difference is that middleware will run for all endpoints and pipeline behaviors depending how they are configured, can run to hybrid or mediator endpoints only. 

Another important difference is that pipeline behaviors execute after all middlewares next() call, and before middleware return path. We could say behaviors wrap the endpoint and middleware pipeline wraps the behaviors.


### Adding IPipelineBehavior for Hybrid Enpdoints

In hybrid endpoints resolve IRequestDispatcher instead of IRequestHandler.

```csharp
// Register the IPipelineBehavior<,>
builder.App.HostBuilder
    .ConfigureServices((_, services) =>
    {
        services.AddScoped(typeof(IPipelineBehavior<,>), typeof(ExampleBehavior<,>));
    });

( ... )
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

IPipelineBehavior also supports void response just like IRequestHandler

```csharp
builder.ConfigureServices((_, services) =>
{
    services
        .AddScoped(typeof(IPipelineBehaviorNoResponse<>), typeof(ExampleBehavior<>))
});

( ... )
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

### Adding IPipelineBehavior for Mediator Endpoints

For mediator endpoints, the framework already resolves the IRequestDispatcher

```csharp
// Register the IPipelineBehavior<>
builder.App.HostBuilder
    .ConfigureServices((_, services) =>
    {
        services.AddScoped(typeof(IPipelineBehavior<>), typeof(ExampleBehavior<>));
    });

( ... )

// Define the endpoint
[Route("GET", "/mediator-endpoint")]
public class ContextHandlerExample(DependencyService service) : IContextHandler<Http11Context>
{
    public async Task Handle(Http11Context context, CancellationToken cancellationToken)
    {
        await Task.Delay(0, cancellationToken); // Do work

        service.Handle();

        context
            .Respond()
            .Status(ResponseStatus.Ok)
            .Type("application/json")
            .Content(new JsonContent(new { Name = "Toni" }, JsonSerializerOptions.Default));
    }
}

// Define the IPipelineBehavior<TContext>
public class ExampleBehavior<TContext> : IPipelineBehavior<TContext>
    where TContext : IContext
{
    public async Task Handle(TContext context, RequestHandlerDelegate next, CancellationToken cancellationToken)
    {
        // Execute pre endpoint behavior logic here
        
        // Execute next IPipelineBehavior or IContextHandler
        await next();

        // Execute post endpoint behavior logic here
    }
}
```

or define TContext if known

```csharp
// Register the IPipelineBehavior<>
builder.App.HostBuilder
    .ConfigureServices((_, services) =>
    {
        services.AddScoped(typeof(IPipelineBehavior<Http11Context>), typeof(ExampleBehavior));
    });

( ... )

// Define the endpoint
[Route("GET", "/mediator-endpoint")]
public class ContextHandlerExample(DependencyService service) : IContextHandler<Http11Context>
{
    public async Task Handle(Http11Context context, CancellationToken cancellationToken)
    {
        await Task.Delay(0, cancellationToken); // Do work

        service.Handle();

        context
            .Respond()
            .Status(ResponseStatus.Ok)
            .Type("application/json")
            .Content(new JsonContent(new { Name = "Toni" }, JsonSerializerOptions.Default));
    }
}

// Define the IPipelineBehavior<TContext>
public class ExampleBehavior : IPipelineBehavior<Http11Context>
{
    public async Task Handle(Http11Context context, RequestHandlerDelegate next, CancellationToken cancellationToken)
    {
        // Execute pre endpoint behavior logic here
        
        // Execute next IPipelineBehavior or IContextHandler
        await next();

        // Execute post endpoint behavior logic here
    }
}
```
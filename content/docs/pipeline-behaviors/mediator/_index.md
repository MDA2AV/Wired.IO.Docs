---
title: 4.2 Mediator Endpoints
type: docs
sidebar:
  open: false
---

### Adding IPipelineBehavior for Mediator Endpoints

For mediator endpoints, the framework already resolves the IRequestDispatcher

```csharp
// Register the IPipelineBehavior<>
builder.Services.AddScoped(typeof(IPipelineBehavior<>), typeof(ExampleBehavior<>));

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

or define TContext if known, this can also be taken advantage to filter behaviors on TContext.

```csharp
// Register the IPipelineBehavior<>
builder.Services.AddScoped(typeof(IPipelineBehavior<Http11Context>), typeof(ExampleBehavior));

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
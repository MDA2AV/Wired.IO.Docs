---
title: 5. Dependency Injection
type: docs
sidebar:
  open: false
---

Wired.IO supports dependency injection across the entire framework.

### Registering a service

Register @ Wired.IO internal IHostBuilder (HostBuilder)

```csharp
( ... )
builder.App.HostBuilder
    .ConfigureServices((_, services) =>
    {
        services.AddScoped<DependencyService>()
    });
( ... )
builder.Build(); // Builds IHostBuilder into IHost
( ... )

public class DependencyService(ILogger<DependencyService> logger) : IDisposable
{
    public void Handle() =>
        logger.LogInformation($"{nameof(DependencyService)} was handled.");
    public void Dispose() =>
        logger.LogInformation($"{nameof(DependencyService)} was disposed.");
}
```

{{< tabs items="Fast Endpoints,Hybrid Endpoints,Mediator Endpoints" >}}

{{< tab >}}

#### Fast Endpoints

Resolve dependencies directly from scope (IServiceProvider).

scope is AsyncServiceScope meaning that by the end of the request, all scoped lifetime dependencies resolved during the request, will be disposed.

```csharp
.MapGet("/fast-endpoint", scope => async httpContext =>
{
    var dependency = scope.GetRequiredService<DependencyService>();
    dependency.Handle(); // Use the service

    httpContext
        .Respond()
        .Status(ResponseStatus.Ok)
        .Type("application/json")
        .Content(new JsonContent(
            new { Name = "Alice", Age = 30 }, 
            JsonSerializerOptions.Default));
})
```
{{< /tab >}}

{{< tab >}}
#### Hybrid Endpoints

Resolve dependencies directly from scope

```csharp
( ... )
.MapGet("/hybrid-endpoint", scope => async context =>
{
    // Resolve the IRequestHandler
    var requestHandler = scope.GetRequiredService<IRequestHandler<RequestQuery, RequestResult>>();

    var result = await requestHandler.Handle(new RequestQuery(), context.CancellationToken);

    context
        .Respond()
        .Status(ResponseStatus.Ok)
        .Type("application/json")
        .Content(new JsonContent(result, JsonSerializerOptions.Default));
})
.Build();
( ... )

//@ IRequestHandler
// Injecting DependencyService @ constructor
public class RequestHandlerExample(DependencyService dependencyService) : IRequestHandler<RequestQuery, RequestResult>
{
    public async Task<RequestResult> Handle(RequestQuery request, CancellationToken cancellationToken)
    {
        // Execute injected dependency logic
        dependencyService.Handle();

        return new RequestResult("Toni", "Mars");
    }
}
public record RequestQuery() : IRequest<RequestResult>;

public record RequestResult(string Name, string Address);

```
{{< /tab >}}

{{< tab >}}
#### Mediator Endpoints

Inject dependencies from the constructor or IContext.Scope.ServiceProvider

```csharp
[Route("GET", "/mediator-endpoint")]
public class ContextHandlerExample(DependencyService service) : IContextHandler<Http11Context>
{
    public async Task Handle(Http11Context context, CancellationToken cancellationToken)
    {
        // Can also resolve from context
        // var service = context.Scope.ServiceProvider.GetRequiredService<DependencyService>();
        // This would be consider anti patternish so avoid.

        await Task.Delay(0, cancellationToken); // Do work

        service.Handle();

        context
            .Respond()
            .Status(ResponseStatus.Ok)
            .Type("application/json")
            .Content(new JsonContent(new { Name = "Toni" }, JsonSerializerOptions.Default));
    }
}
```
{{< /tab >}}
{{< /tabs >}}


### Resolving dependencies for Middleware and IPipelineBehaviors

For Middleware, resolve directly from scope, see *3. Adding Middleware* chapter.

For IPipelineBehaviors, resolve directly from constructor or context.scope.
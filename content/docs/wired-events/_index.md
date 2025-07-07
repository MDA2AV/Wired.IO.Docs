---
title: 9. Wired Events
type: docs
sidebar:
  open: false
---

Wired events, similar to domain events concept provide a scalable, extensible way to trigger side effects, notifications, or further workflows. 

### IContext Wired Events

Instead of living the the domain entity, wired events live in the IContext which is uniquer for each request.

```csharp
// Create a IWiredEvent
public class ExampleWiredEvent(string description) : IWiredEvent
{
    public Guid Id { get; } = Guid.NewGuid();
    public DateTime OccurredOn { get; } = DateTime.UtcNow;
    public string Description { get; } = description;
}

// Create a IWiredEventHandler<in TEvent> where TEvent : IWiredEvent
public class ExampleWiredEventHandler(ILogger<ExampleWiredEventHandler> logger) : IWiredEventHandler<ExampleWiredEvent>
{
    public async Task HandleAsync(ExampleWiredEvent wiredEvent)
    {
        // Handle the wired event here
        logger.LogInformation($"Handled wired event: {wiredEvent.Description} at {wiredEvent.OccurredOn}");
    }
}

( ... )
// Register the IWiredEventHandler in the IoC container
builder.App.HostBuilder
    .ConfigureServices((_, services) =>
    {
        services
            ( ... )
            .AddWiredEventHandler<ExampleWiredEvent, ExampleWiredEventHandler>()
            ( ... )
    });
( ... )

// Create wired events that will be handled by the middleware after the request is processed
( ... )
builder
    .AddWiredEvents(dispatchContextWiredEvents: true)
    .MapGet("/wired-event", scope => async context =>
    {
        context.AddWiredEvent(new ExampleWiredEvent("Creating a wired event"));

        // Handle response here..
    })
( ... )
```

### Non IContext Wired Events

In many scenarios IContext is not practical to own the wired events such as when creating events in different application layers. For those cases it is possible to manually handle the wired events dispatching. One of the greatest advantage of using wired events is allowing any class to delegate work to be done by a handler that is registeres in the IoC while the class that creates the event itself isn't.

This is specifically useful when working with transactions such as is database access where the database models are entities that cannot inject dependencies in their constructors.

#### Create the wired event, handler and the class that will own the wired events
```csharp
// Create a IWiredEvent
public class ExampleWiredEvent(string description) : IWiredEvent
{
    public Guid Id { get; } = Guid.NewGuid();

    public DateTime OccurredOn { get; } = DateTime.UtcNow;

    public string Description { get; } = description;
}
// Create a IWiredEventHandler<in TEvent> where TEvent : IWiredEvent
public class ExampleWiredEventHandler(EmailService emailService, ILogger<ExampleWiredEventHandler> logger) : IWiredEventHandler<ExampleWiredEvent>
{
    // The handler can inject services from the IoC container
    public async Task HandleAsync(ExampleWiredEvent wiredEvent)
    {
        // Handle the wired event here
        emailService.SendEmail("Something happened", "wiredio@gmail.com");
        logger.LogInformation($"Handled wired event: {wiredEvent.Description} at {wiredEvent.OccurredOn}");
    }
}
// Create a class that implements IHasWiredEvents
public class Entity : IHasWiredEvents
{
    private readonly List<IWiredEvent> _wiredEvents = new();
    public IReadOnlyList<IWiredEvent> WiredEvents => _wiredEvents;
    public void AddWiredEvent(IWiredEvent wiredEvent) => _wiredEvents.Add(wiredEvent);
    public void ClearWiredEvents() => _wiredEvents.Clear();

    public void DoSomething()
    {
        // Simulate some operation that generates a wired event
        // For example, storing it in a database
        var wiredEvent = new ExampleWiredEvent("Entity did something important");
        AddWiredEvent(wiredEvent);
    }
}
```

#### Register the wired event handler
```csharp
( ... )
// Register the IWiredEventHandler in the IoC container
builder.App.HostBuilder
    .ConfigureServices((_, services) =>
    {
        services
            ( ... )
            .AddWiredEventHandler<ExampleWiredEvent, ExampleWiredEventHandler>()
            ( ... )
    });
( ... )
```

#### At the endpoint..

{{< tabs items="Fast Endpoints,Hybrid Endpoints,Mediator Endpoints" >}}

{{< tab >}}

```csharp
// Create Entity and manually dispatch the wired events 
builder
    // dispatchContextWiredEvents can be set as false 
    // if context wired events are not being used
    .AddWiredEvents(dispatchContextWiredEvents: false)
    .MapGet("/wired-event", scope => async httpContext =>
    {
        var entity = new Entity();
        entity.DoSomething();

        // Handle response here..

        // Dispatch wired events, this can be wrapped 
        // in a unit of work pattern or outbox pattern
        var wiredEventDispatcher = scope
            .GetRequiredService<Func<IEnumerable<IWiredEvent>, Task>>();
        await wiredEventDispatcher(entity.WiredEvents);
        entity.ClearWiredEvents();
    })
```
{{< /tab >}}

{{< tab >}}
```csharp
public class RequestHandlerExample(Func<IEnumerable<IWiredEvent>, Task> wiredEventDispatcher) 
    : IRequestHandler<RequestQuery, RequestResult>
{
    public async Task<RequestResult> Handle(RequestQuery request, CancellationToken cancellationToken)
    {
        var entity = new Entity();
        entity.DoSomething();

        await wiredEventDispatcher(entity.WiredEvents);
        entity.ClearWiredEvents();

        return new RequestResult("Toni", "Mars");
    }
}
public record RequestQuery() : IRequest<RequestResult>;

public record RequestResult(string Name, string Address);

( ... )
builder
    .AddWiredEvents(dispatchContextWiredEvents: false)
    .MapGet("/wired-event-handler", scope => async context =>
    {
        var requestHandler = scope.GetRequiredService<IRequestDispatcher<Http11Context>>();
        var result = await requestHandler.Send(new RequestQuery(), context.CancellationToken);

        context
            .Respond()
            .Status(ResponseStatus.Ok)
            .Type("application/json")
            .Content(new JsonContent(result, JsonSerializerOptions.Default));
    })
( ... )
```
{{< /tab >}}

{{< tab >}}
```csharp
// The most elegant solution
[Route("GET", "/wired-event-handler")]
public class ContextHandlerExample(Func<IEnumerable<IWiredEvent>, Task> wiredEventDispatcher) 
    : IContextHandler<Http11Context>
{
    public async Task Handle(Http11Context context, CancellationToken cancellationToken)
    {
        var entity = new Entity();
        entity.DoSomething();

        await wiredEventDispatcher(entity.WiredEvents);
        entity.ClearWiredEvents();

        context
            .Respond()
            .Status(ResponseStatus.Ok)
            .Type("application/json")
            .Content(new JsonContent(new { Name = "Toni", Address = "Mars" },
                JsonSerializerOptions.Default));
    }
}
```
{{< /tab >}}

{{< /tabs >}}
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
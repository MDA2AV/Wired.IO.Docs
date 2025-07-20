---
title: Introduction
weight: 1
type: docs
sidebar:
  open: false
---

In this documentation you can find instructions in how to use and take advantage of Wired.IO. In many of the code snipets you will find "( ... )" meaning usually some boiler plate is missing in the example, refer to previous sections when in doubt. The preferred order to create a Wired.IO webserver is create the builder, register builder services (@ builder.Services) and then builder configs such as fast endpoints, Tls, Port, Endpoint, etc, followed by .Build() and running or starting it, depending whether you want to block or not.

Wired.IO webservers can be used in standalone or embedded with existing applications, when embedded with existing applications, you can integrating the existing IServiceCollectin and IServiceProvider in your Wired.IO webserver to share the same IoC container. See *Embedding with Existing Applications* section for more information.

**If you need help or any clarification don't hesitate in contacting us via our discord that you can find at the top if this website.**

```csharp
// Create the Builder
var builder = WiredApp.CreateBuilder();

// Register Services, Events handlers etc..
builder.Services
    .AddScoped<DependencyService>()
    .AddWiredEventHandler<ExampleWiredEvent, ExampleWiredEventHandler>()
    .AddScoped(typeof(IPipelineBehavior<,>), typeof(ExampleBehavior<,>));

// Builder configurations
builder
    .AddHandlers(Assembly.GetExecutingAssembly()) // If using hybrid or mediator endpoints
    .Port(5000)
    .MapGet("/quick-start", scope => async httpContext =>
    {
        httpContext
            .Respond()
            .Status(ResponseStatus.Ok)
            .Type("text/plain")
            .Content(new StringContent("Hello!"));
    });

// Create and start/run the app
var app = builder.Build();
await app.RunAsync();
```
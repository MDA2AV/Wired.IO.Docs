---
title: Create a Builder
weight: 3
type: docs
sidebar:
  open: false
---


In this section you will learn how to create a builder using the default or a custom handler.

Handlers are responsible of receiving the request, parse it and build the **context** that can be used in the endpoint logic to properly handle the request.

Check out Types section to learn more about this **context**.


### Builder Configurability

```csharp

// Create a default builder
var builder = WiredApp.CreateBuilder();


builder
  // Sets the port number
  .Port(5000)

  // Sets IPAddress and Port
  .Endpoint( ... )

  // Sets the backlog (max number of concurrent connection)
  .Backlog(128)

  // Define TLS options
  // See 8 Security (TLS)
  .UseTls( ... )

  // Register all handlers in the IServiceConnection
  // See 2.2 Mediator Endpoints
  .AddHandlers( ... )

  // Register wired event disptacher to the IServiceCollection
  .AddWiredEvents()
  // Do not automatically dispatch context wired events
  .AddWiredEvents(false)

  // Register custom middleware
  // See 3. Adding Middleware
  .UseMiddleware( ... )

  // Register fast endpoints
  .MapGet( ... )
  .MapPost( ... )
  .MapPut( ... )
  .MapDelete( ... )
  .MapPatch( ... )
  .MapHead( ... )
  .MapOptions( ... )


```
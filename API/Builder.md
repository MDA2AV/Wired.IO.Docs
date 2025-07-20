---
title: Builder<THandler, TContext> Class
weight: 4
type: docs
sidebar:
  open: false
---

The `Builder<THandler, TContext>` class provides a fluent API to configure and build a `WiredApp<TContext>` instance. It enables setting up HTTP handlers, TLS options, endpoints, dependency injection, middleware, and route mappings in a structured and composable manner.

## Declaration

```csharp
public sealed class Builder<THandler, TContext>
    where THandler : IHttpHandler<TContext>
    where TContext : IContext
```


## Purpose

This builder is the main entry point for configuring a **Wired.IO** application. It encapsulates:
- Dependency injection configuration
- HTTP handler setup
- Middleware registration
- Route-to-handler mappings
- TLS and network settings
- Domain event dispatching

Once configuration is complete, calling `Build()` produces a fully initialized `WiredApp<TContext>`.


## Constructor Overloads

```csharp
public Builder(Func<THandler> handlerFactory)
public Builder(Func<THandler> handlerFactory, List<SslApplicationProtocol> sslApplicationProtocols)
```

- Initializes the builder with a user-defined HTTP handler factory.
- ALPN protocols default to HTTP/1.1 unless explicitly provided.


## Key Methods

### `Build(IServiceProvider? serviceProvider = null)`

Finalizes the builder, creates the service provider (if not provided), resolves all middleware and route handlers, and returns a `WiredApp<TContext>` instance.


### `UseTls(SslServerAuthenticationOptions options)`

Enables TLS with the provided configuration.


### `Endpoint(...)` / `Port(...)` / `Backlog(...)`

Configure the IP address and port to bind the server socket, and the TCP backlog.


### `EmbedServices(IServiceCollection services)`

Replaces the internal `ServiceCollection` and injects default middleware.


### `AddHandlers(Assembly assembly)`

Registers all route handlers from a given assembly and adds an internal request dispatcher.


### `AddWiredEvents(bool = true)`

Adds support for dispatching `WiredEvents` after request execution. Optionally hooks up post-pipeline auto-dispatch logic.


### `UseMiddleware(...)`

Registers scoped request or response middleware with dependency injection.


### `Map[HttpMethod](route, handlerFactory)`

Maps routes to scoped handlers for all standard HTTP methods: `GET`, `POST`, `PUT`, `DELETE`, `PATCH`, `HEAD`, `OPTIONS`, `TRACE`, `CONNECT`.


## Middleware and Routing

Handlers and middleware are registered via `Func<IServiceProvider, ...>` factories, supporting scoped lifetimes per request.

- Middleware: `Func<TContext, Func<TContext, Task>, Task>`
- Response middleware: `Func<TContext, Func<TContext, Task<IResponse>>, Task<IResponse>>`
- Handlers: `Func<TContext, Task>` or `Action<TContext>`

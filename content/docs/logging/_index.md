---
title: 6. Logging
type: docs
sidebar:
  open: false
---

Wired.IO implements ILogger (Microsoft.Extensions.Logging), logging options are configurable through the builder.

```csharp
( ... )
builder.App.HostBuilder
    .ConfigureLogging(logging =>
    {
        // Configure ILoggingBuilder
    })
( ... )
```

Check official docs at 
[ILoggingBuilder docs](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.logging.iloggingbuilder?view=net-9.0-pp)
to explore configurability.

#### Inject an ILogger

Resolve a ILogger<> anywhere.

For fast endpoints and middleware resolve directly from scope.

```csharp
// Replace Program with your TCategoryName
var logger = scope.GetRequiredService<ILogger<Program>();
```

Anyhwere else, resolve from constructor.

```csharp
// For example in a IRequestHandler
public class RequestHandlerExample(ILogger<RequestHandlerExample> logger) 
    : IRequestHandler<RequestQuery, RequestResult>
{
    public async Task<RequestResult> Handle(RequestQuery request, CancellationToken cancellationToken)
    {
        logger.LogInformation($"{nameof(RequestHandlerExample)} was handled.");

        ( ... )
    }
}
```
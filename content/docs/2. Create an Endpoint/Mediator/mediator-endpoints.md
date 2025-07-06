---
title: 2.2.2 - Mediator Endpoints
type: docs
---

*Note: In this section we will use the default inbuilt HTTP/1.1 protocol handler.*

Mediator endpoints do not require the creation of a fast endpoint.

Unlike hybrid endpoints that implement IRequestHandler, mediator endpoints implement IContextHandler since the context is passed directly to the Handle method.

In short, the logic that would be inside the fast endpoint, is now in the Handle method.

```csharp
( ... )
var app = builder

    // Call this to tell the builder to scan assembly and 
    // register your IContextHandler types
    .AddHandlers(Assembly.GetExecutingAssembly())

    .Port(5000) // Configured to http://localhost:5000
( ... )

// The routing is defined with the RouteAttribute, 
// this is mandatory or else it won't be routed
[Route("GET", "/mediator-endpoint")]
public class ContextHandlerExample() 
    // Http11Context is used as we are using the default inbuilt IHttpHandler
    // Adapt to your custom handler if the case
    : IContextHandler<Http11Context>
{
    public async Task Handle(Http11Context context, CancellationToken cancellationToken)
    {
        await Task.Delay(0, cancellationToken); // Do work

        // Again, using the middleware response pipeline is optional here
        context
            .Respond()
            .Status(ResponseStatus.Ok)
            .Type("application/json")
            .Content(new JsonContent(new { Name = "Toni" }, JsonSerializerOptions.Default));
    
    }
}

```
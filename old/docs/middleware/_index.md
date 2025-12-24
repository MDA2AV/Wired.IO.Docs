---
title: Adding Middleware
weight: 5
type: docs
sidebar:
  open: false
---

*Note: In this section we will use the default inbuilt HTTP/1.1 protocol handler.*

Create and register custom middleware to the request handling pipeline, analogous to ASP.NET Core. Note that the middlewares will execute by registration order.

In this section let's create a basic global error handling middleware.

```csharp
( ... )
builder.UseMiddleware(scope => async (context, next) =>
{
    // logger or any dependencies can be resolved using scope
    var logger = scope.GetRequiredService<ILogger<Program>>();

    try
    {
        // Execute next in line, could be another middleware or the endpoint
        await next(context);
    }
    // If unhandled exception caught, create an error response
    // to be processed by the response handling middleware,
    // the response handling middleware will always run after
    // any user registered middleware as it is always registered first.
    catch (Exception e)
    {
        logger.LogError(e.Message);

        context.Respond()
            .Status(ResponseStatus.InternalServerError)
            .Type("application/json")
            .Content(
                new JsonContent(new { Error = e.Message }, 
                JsonSerializerOptions.Default));
    }
})
( ... )
```
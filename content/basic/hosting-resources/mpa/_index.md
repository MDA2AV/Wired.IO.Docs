---
title: Multi Page Application
---

In this example we host a MPA (embedded resources) placed inside the Docs folder.

Adapt your Location object to configure the resource origin.

```csharp
using System.Reflection;
using Wired.IO.App;
using Wired.IO.Http11Express.StaticHandlers;

await WiredApp
    .CreateExpressBuilder()
    .Port(8080)
    .AddMpaProvider("/*",
        new Location
        {
            Assembly = Assembly.GetExecutingAssembly(),
            LocationType = LocationType.EmbeddedResource,
            Path = "Docs"
        }, []) // Pass middleware collection here
    .Build()
    .RunAsync();
```

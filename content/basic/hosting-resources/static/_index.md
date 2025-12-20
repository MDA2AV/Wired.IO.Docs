---
title: Static Resources
---

Hosting static embedded resources.

For example if you add a embedded resource at the Docs folder (e.g. Docs/data.txt), you can retrieve that resource from: http://localhost:8080/resources/data.txt.

Adapt your Location object to configure the resource origin.

```csharp
using System.Reflection;
using Wired.IO.App;
using Wired.IO.Http11Express.StaticHandlers;

await WiredApp
    .CreateExpressBuilder()
    .Port(8080)
    .AddStaticResourceProvider("/resources/*",
        new Location
        {
            Assembly = Assembly.GetExecutingAssembly(),
            LocationType = LocationType.EmbeddedResource,
            Path = "Docs"
        }, []) // Pass middleware collection here
    .Build()
    .RunAsync();
```

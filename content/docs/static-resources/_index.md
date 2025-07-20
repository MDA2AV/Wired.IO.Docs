---
title: Static Resources
weight: 12
type: docs
sidebar:
  open: false
---

Using the default IHttpHandler (see *1. Create a Builder*) it is very straightforward to serve static resources.

The static resources build action must be *Embedded Resource*.

When creating the builder pass the assembly in which the resources are contained as the full embeddedd resource path.

```csharp
var builderWithParameters = App.CreateBuilder(() => 
    new WiredHttp11<Http11Context>(new Http11HandlerArgs(
        UseResources: true,
        // Full resources path for example:
        // If the resources are stored in the project root just pass the Assembly name
        // If the resources are stored in side a folder, pass the full pass e.g. MyAssemblyName.FolderName
        ResourcesPath: "MyAssemblyName",
        // The assembly where the resources are stored
        ResourcesAssembly: Assembly.GetExecutingAssembly()))
);
```

 All set, to test this just send a request with the resource name as route for example: http://localhost:5000/index.html
---
title: 1.1 Default Handler
type: docs
---

By creating a builder with default handler, the inbuilt HTTP/1.1 protocol handler is set with default parameters.

```csharp
using Wired.IO.App;

var builder = WiredApp.CreateBuilder();
```

#### Inject IHandlerArgs (default values)

By passing *UseResources: false*, *ResourcesPath* and *ResourcesAssembly* will not be considered.

Check **Resource Hosting** section to learn more about these parameters.

```csharp
using Wired.IO.App;
using Wired.IO.Protocol.Handlers;
using Wired.IO.Http11.Context;

var builderWithParameters = WiredApp.CreateBuilder(() => 
    new WiredHttp11<Http11Context>(new Http11HandlerArgs(
        UseResources: false,
        ResourcesPath: null!,
        ResourcesAssembly: null!))
);
```

#### Inject accepted SslApplicationProtocols

For the default handler case, SslApplicationProtocol.Http11 will be considered by default, but that configuraiton can be overrided by injecting it in the CreateBuilder method.

```csharp
using Wired.IO.App;
using System.Net.Security;

var builderWithSslApplicationProtocols = WiredApp.CreateBuilder([SslApplicationProtocol.Http11]);
```

#### Inject IHandlerArgs and accepted SslApplicationProtocols

```csharp
using Wired.IO.App;
using Wired.IO.Protocol.Handlers;
using Wired.IO.Http11.Context;
using System.Net.Security;

var builder = WiredApp.CreateBuilder(() =>
    new WiredHttp11<Http11Context>(new Http11HandlerArgs(
        UseResources: false,
        ResourcesPath: null!,
        ResourcesAssembly: null!)), [SslApplicationProtocol.Http11]
);
```
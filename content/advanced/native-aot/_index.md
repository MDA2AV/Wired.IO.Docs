---
title: Native AoT
weight: 1
---

Wired.IO supports native AoT builds by default as the framework does not use reflection.

If you are planning on build a native AoT app and using serialization there are some remarks.

For Json serialization use ExpressJsonAotContent instead of ExpressJsonContent.

```csharp
using System.Text.Json.Serialization;
using Wired.IO.App;
using Wired.IO.Http11Express.Response.Content;
using Wired.IO.Protocol.Response;

var builder = WiredApp
    .CreateExpressBuilder()
    .Port(8080);
    
builder
    .MapGroup("/")
    .MapGet("/my-endpoint", context =>
    {
        JsonContext SerializerContext = JsonContext.Default;
        
        context
            .Respond()
            .Status(ResponseStatus.Ok)
            .Type("application/json"u8)
            .Content(new ExpressJsonAotContent(new JsonMessage
            {
                Message = "Hello World!"
            }, SerializerContext.JsonMessage));
    });

await builder
    .Build()
    .RunAsync();
    
public struct JsonMessage { public string Message { get; set; } }

[JsonSourceGenerationOptions(GenerationMode = JsonSourceGenerationMode.Serialization | JsonSourceGenerationMode.Metadata)]
[JsonSerializable(typeof(JsonMessage))]
public partial class JsonContext : JsonSerializerContext { }
```
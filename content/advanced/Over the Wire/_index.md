---
title: Over the Wire
weight: 3
---

Wired.IO grants direct access to the TCP connection's PipeReader and PipeWriter, perfect for very high performance applications. This trait also means that full duplex communication between server and client as well as streaming (Server Sent Events) or ANY other custom form or direct communication are easily implemented.


```csharp
using System.Buffers;
using Wired.IO.App;

var builder = WiredApp
    .CreateExpressBuilder()
    .Port(8080);

builder
    .MapGroup("/")
    .MapGet("/my-endpoint*", async context =>
    {
        var pipeReader = context.Reader;
        var pipeWriter = context.Writer;
        
        // To read from the client
        var clientRequest = await pipeReader.ReadAsync();
        var data = clientRequest.Buffer.ToArray();
        
        // To write and flush to the client
        //
        // Write multiple and flush once
        pipeWriter.Write("Hello "u8);
        pipeWriter.Write("World!"u8);
        await pipeWriter.FlushAsync();
        //
        // Write and flush
        await pipeWriter.WriteAsync("Hello World"u8.ToArray());
    });
    
await builder
    .Build()
    .RunAsync();
```
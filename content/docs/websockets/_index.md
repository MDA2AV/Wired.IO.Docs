---
title: 7. Websockets
type: docs
sidebar:
  open: false
---

**Wired.IO internal default http handler (WiredHttp11) implements RFC 6455.**

When using a custom IHttpHandler, you must implement it yourself. Check *1. Create a Builder* section to learn more about IHttpHandler.

While using fast endpoints is the preferred option to use websockets, it is not mandatory, anywhere where you have access to the IContext you can take advantage of the IContext extension methods to write to or read from the websocket.

Note: when using websockets Wired.IO's WiredHttp11 is expecting a ws:// request or wss:// if tls is enabled.

Check *8. Security (TLS)* chapter to learn more about how to use *Transport Layer Security*.

```csharp
( ... )
// ws://{hostname:portnumber}/websocket
// or
// wss://{hostname:portnumber}/websocket
// if TLS is enabled
.MapGet("/websocket", scope => async context =>
{
    // Optional, rent an array pool or just allocate a new array.
    var arrayPool = ArrayPool<byte>.Shared;
    var buffer = arrayPool.Rent(8192);

    while (true)
    {
        // Use IContext extensions WsReadAsync and WsSendAsync
        // to read from and send data to the websocket.

        (ReadOnlyMemory<byte> data, WsFrameType wsFrameType) receivedData 
            = await context.WsReadAsync(buffer);

        if (receivedData.wsFrameType == WsFrameType.Close)
            break;
        if (receivedData.data.IsEmpty)
            break;

        await context.WsSendAsync(receivedData.Item1, 0x01);
    }

    // Optional, return the rented buffer if applicable.
    arrayPool.Return(buffer);
})
( ... )
```
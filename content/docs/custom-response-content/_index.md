---
title: B. Custom Response Content
type: docs
sidebar:
  open: false
---

*Note: this feature is part of the Wired.IO default IHttpHandler middleware pipeline (Response Middleware)*
See *1. Create a Builder* to learn more about IHttpHandler.

When using the response middleware, for example:

```csharp
// Example with fast endpoint

builder
    .MapGet("/route", scope => context =>
    {
        context
            .Respond()
            .Status(ResponseStatus.Ok)
            .Type("application/json")
            .Content(new JsonContent(new { Name = "Example" },
            JsonSerializerOptions.Default));
    })
```

This will create an IResponse in the IContext (context) that will be processed by the ResponseMiddleware, if IResponse is null, the middleware is skipped (for the cases when the user handles the response himself).

### Create a custom IResponseContent

With custom IResponseContent it is possible to gain some more control over the response content while using response middleware.

{{< tabs items="Chunked Transfer Encoding, Content-Length-Based Response" >}}

{{< tab >}}

Chunked responses can be handy when content length is unknown or make data availabe to the client without having to wait for the full content.

```csharp
public class CustomResponseContent(ReadOnlyMemory<byte> data) : IResponseContent
{
    // Set Length to null to let the response middleware know to use
    // transfer chunked encoding
    // If Length is set, the Content-Length will be set to its value
    public ulong? Length => null!;

    public ValueTask<ulong?> CalculateChecksumAsync() => new((ulong)data.GetHashCode());

    // Define the chunked Write
   public async ValueTask WriteAsync(ChunkedPipeWriter writer, uint bufferSize)
    {
        // Access the underlying PipeWriter if need
        // ChunkedPipeWriter is just a wrapper on PipeWriter
        // to write in chunks (add chunk size and chunk terminators)
        var underlyingPipeWriter = writer.GetPipeWriter();

        // Send first chunk and flush it so that receiver can already process it
        // For NET9.0+ you can use await writer.WriteAsync(data)
        // already flushes
        writer.Write(data.Span);
        await writer.FlushAsync();

        // Send second chunk
        writer.Write("Additional information"u8);

        // Send chunked response terminator and flush it
        writer.Finish();
        await writer.FlushAsync();
    }

    // Length is null, this method will never be used
    public void Write(PlainPipeWriter writer, uint bufferSize)
    {
        throw new NotImplementedException();
    }
}
```

This is what the response looks like from client side:

```
HTTP/1.1 200 OK
Server: Wired.IO
Date: Mon, 07 Jul 2025 17:10:18 GMT
Content-Type: text/plain
Transfer-Encoding: chunked

D
response data
16
Additional information
0


```

{{< /tab >}}

{{< tab >}}

When using content length based response, Length property cannot be null.

```csharp
public class CustomResponseContent(ReadOnlyMemory<byte> data) : IResponseContent
{
    // Set the Length property
    public ulong? Length => (ulong)data.Length;

    public ValueTask<ulong?> CalculateChecksumAsync() => new((ulong)data.GetHashCode());

    // Length is not null, this method will never be used
    public async ValueTask WriteAsync(ChunkedPipeWriter writer, uint bufferSize)
    {
        throw new NotImplementedException();
    }

    public async ValueTask WriteAsync(PipeWriter writer, uint bufferSize)
    {
        // For NET9.0+ you can use await writer.WriteAsync(data)
        // already flushes
        writer.Write(data.Span);
        await writer.FlushAsync();
    }
}
```

This is what the response looks like on the client side:

```
HTTP/1.1 200 OK
Server: Wired.IO
Date: Mon, 07 Jul 2025 21:23:49 GMT
Content-Type: text/plain
Content-Length: 13

response data

```

{{< /tab >}}

{{< /tabs >}}
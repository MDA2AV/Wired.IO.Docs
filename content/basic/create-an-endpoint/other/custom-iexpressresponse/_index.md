---
title: Custom IResponseContent
---

Create a custom IResponseContent that handles the content/body writing.

Note that if the Length property is null, the framework will set the "Transfer-Encoding" header to "chunked", meaning that you need to use the ChunkedWriter to write.

```csharp
using System.Buffers;
using System.IO.Pipelines;
using Wired.IO.App;
using Wired.IO.Http11Express.Response.Content;

var builder = WiredApp
    .CreateExpressBuilder()
    .Port(8080);

builder
    .MapGroup("/")
    .MapGet("/my-endpoint", context =>
    {
        context
            .Respond()
            .Type("application/octet-stream"u8)
            .Content(new CustomResponseContent("My endpoint!"u8.ToArray()));
    });
    
await builder
    .Build()
    .RunAsync();
    

public class CustomResponseContent : IExpressResponseContent
{
    private ReadOnlyMemory<byte>  _data;
    public ulong? Length { get; }

    public CustomResponseContent(ReadOnlyMemory<byte> data)
    {
        _data = data; 
        Length = (ulong)data.Length;
    }
    
    public void Write(PipeWriter writer)
    {
        writer.Write(_data.Span);
    }
}
```
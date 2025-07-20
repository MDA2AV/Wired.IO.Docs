---
title: IResponse
type: docs
sidebar:
  open: false
---

`IResponse` defines the structure of an HTTP response to be sent to the client in **Wired.IO**. It contains protocol metadata, headers, and content information used to construct and stream the response back over the connection.


## Declaration

```csharp
public interface IResponse : IDisposable
```


## Properties

### Protocol


### `FlexibleResponseStatus Status`
Gets or sets the HTTP response status code.


## Headers


### `DateTime? Expires`
Defines when this resource will expire.


### `DateTime? Modified`
Defines when this resource was last modified.


### `string? this[string field]`
Gets or sets the value of an individual header field by name.

**Parameters:**
- `field`: The name of the header field.

**Returns:**
- The value of the specified header, or `null` if not set.


### `IEditableHeaderCollection Headers`
Gets the complete collection of HTTP response headers.


## Content


### `IResponseContent? Content`
Gets or sets the response body.

**Remarks:**
> This content is streamed to the client using either `Content-Length` or chunked transfer encoding.


### `FlexibleContentType? ContentType`
Gets or sets the MIME type of the response (e.g., `"text/html"` or `"application/json"`).


### `string? ContentEncoding`
Gets or sets the encoding used for the response content (e.g., `"br"` for Brotli).


### `ulong? ContentLength`
Gets or sets the length of the response body in bytes.


## Methods


### `void Clear()`
Clears the response state for reuse without disposing it.

**Remarks:**
> Typically used when reusing a context object in a connection handling loop.


## Inherits

- `IDisposable`

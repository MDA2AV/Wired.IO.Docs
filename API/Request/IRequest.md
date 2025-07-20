---
title: IRequest
type: docs
sidebar:
  open: false
---

`IRequest` defines a minimal abstraction of an HTTP request within **Wired.IO**. It exposes core properties such as the route, method, headers, body content, and connection type. This interface is used by the HTTP server to process and route incoming requests.

## Declaration

```csharp
public interface IRequest : IDisposable
```

## Properties

### `string Route`
Gets or sets the route or path portion of the HTTP request.

**Description:**
> Typically used to determine the target endpoint.

### `string HttpMethod`
Gets or sets the HTTP method (e.g., `"GET"`, `"POST"`, `"PUT"`, `"DELETE"`).

**Description:**
> Indicates the action to be performed on the resource.


### `PooledDictionary<string, ReadOnlyMemory<char>>? QueryParameters`
Gets or sets the query string parameters.

**Description:**
> Represents the URL-encoded query parameters following the `?` in the URI.


### `PooledDictionary<string, string> Headers`
Gets or sets the collection of HTTP request headers.

**Description:**
> Each entry is a raw string in the format `"HeaderName: HeaderValue"`.


### `ReadOnlyMemory<byte> Content`
Gets or sets the raw binary content (body) of the HTTP request.

**Remarks:**
> Commonly used in POST/PUT requests to carry JSON, form data, or files.


### `string ContentAsString`
Gets the raw body content interpreted as a UTF-8 string.

**Remarks:**
> This is a convenience property for accessing textual content.  
> May throw if content is not valid UTF-8.


### `ConnectionType ConnectionType`
Gets or sets the type of connection (e.g., `KeepAlive`, `Close`).

**Remarks:**
> Indicates whether the connection should remain open after the response.


## Methods

### `void Clear()`
Clears the request state for reuse without disposing the object.

**Remarks:**
> Useful in connection handling loops where objects are pooled.


## Inherits

- `IDisposable`

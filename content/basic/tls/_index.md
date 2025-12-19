---
title: Security (TLS)
---

Wired.IO supports out of the box secure endpoints.

Visit [SslServerAuthenticationOptions Class](https://learn.microsoft.com/en-us/dotnet/api/system.net.security.sslserverauthenticationoptions?view=net-9.0) for detailed documentation.

```csharp
( ... )
var app = builder
    .UseTls(new SslServerAuthenticationOptions
    {
        // Define your SSL options here
        // Such as load server certificate,
        // Load CA certificate,
        // RemoteCertificateValidationCallback for mutual TLS, etc.
    })
    ( ... )
```
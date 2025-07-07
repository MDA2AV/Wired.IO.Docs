---
title: C. Embedd with Existing Applications
type: docs
sidebar:
  open: false
---

One of the major features of Wired.IO when compared with ASP.NET Core is the possibility of embdedding it in any existing .NET application from console apps to Winforms, WPF, MAUI, AvaloniaUI you name it.

If the existing application does not own a IHost, Wired.IO facilitates by having its own IHost which can be used by the existing app.

For many cases though, the existing application alrady owns a IHost and having multiple IHost instances is usually a no go when the service resolution needs to be centralized.
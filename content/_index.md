---
title: Wire Up the Web - Without the Weight
toc: false
---
<br/>

{{< cards >}}
  {{< card link="docs" title="Docs" icon="book-open" >}}
  {{< card link="quick" title="Quick Start" icon="user" >}}
{{< /cards >}}

# Welcome to Wired.IO

**Wired.IO** is a lightweight, high-performance HTTP server framework for .NET. Designed from the ground up for **embedding**, **extensibility**, and **raw speed**, it gives you full control over your request pipeline without the weight of traditional web frameworks.

Whether you're building APIs, embedded servers, developer tools, or hybrid applications, Wired.IO provides a focused, zero-friction foundation that runs anywhere your .NET code does — no external hosting required.

## Why Wired.IO?

- ⚡ **Fast by default** – Built on `System.IO.Pipelines` and optimized for low allocations and high throughput.
- 🧩 **Fully embeddable** – Add a production-ready HTTP server directly into your desktop, mobile, or console app.
- 🧵 **Lean and composable** – Define only what you need: your context, your pipeline, your handlers.
- 🔧 **Customizable by design** – TLS, routing, DI, and middleware are all open and easily replaceable.
- 🌐 **Hybrid app ready** – Serve a full **web-based frontend** from inside your app. Pair your MAUI or desktop backend with a modern SPA or HTML/JS UI — all self-hosted.
- 🪶 **No runtime magic** – Everything is explicit. No black boxes, no surprises.


## Built for Embedding

Wired.IO was created to **run inside your app**, not alongside it. This means you can:
- Run an HTTP interface inside a background service, tool, or MAUI app.
- Use it for internal tooling, configuration UIs, simulators, or control panels.
- Serve static files, WebSockets, or JSON APIs directly from your executable.
- Create **hybrid apps** with native backends and web-based frontends, served over `localhost`.

## Wired.IO vs ASP.NET Core

While ASP.NET Core is a full-featured, enterprise-grade web framework with extensive tooling, hosting, and middleware infrastructure, Wired.IO is designed for a different purpose: embedding a fast, minimal HTTP server directly into your .NET applications. ASP.NET Core excels in large-scale web applications with MVC, Razor Pages, SignalR, and sophisticated DI features. In contrast, Wired.IO is ideal for lightweight, self-contained workloads — such as background services, developer tools, device-local APIs, hybrid desktop apps, and custom network stacks. It prioritizes performance, composability, and explicit control over convention and abstraction, making it perfect for scenarios where ASP.NET Core is too heavy or invasive.


Whether you're building a lightweight HTTP API, embedding a control panel, or serving a web frontend inside your desktop app, **Wired.IO** gives you the control and performance to do it right — with zero friction.
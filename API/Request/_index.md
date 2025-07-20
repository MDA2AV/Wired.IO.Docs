---
title: Request
weight: 2
type: docs
sidebar:
  open: false
---

A Request in Wired.IO is a lightweight abstraction over an incoming HTTP message. It contains essential routing data such as the HTTP method, URI path, query parameters, and headers. Unlike traditional frameworks that wrap everything in a heavyweight object, Wired.IO keeps the IRequest minimal and efficient, optimized for performance and reusability. It's built to work seamlessly in pipelined or non-blocking environments, where requests may arrive in rapid succession and must be parsed with minimal overhead.
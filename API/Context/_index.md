---
title: Context
weight: 1
type: docs
sidebar:
  open: false
---

In Wired.IO, the context represents the lifecycle and environment for handling a single HTTP request over a connection. It encapsulates everything needed to process a request: the parsed headers and body (Request), the outgoing response (Response), connection-specific streams (PipeReader/PipeWriter), and scoped service resolution (AsyncServiceScope). Contexts are pooled and reused for performance, enabling high-throughput HTTP/1.1 handling with minimal allocations. This abstraction allows middleware and endpoint logic to operate in isolation, while the server infrastructure manages low-level I/O and resource lifetimes.